//git的凭证
def git_auth = "3c624c30-b117-47c8-9e3e-c9551498e3a5"

node {
    stage('拉取代码') {
        checkout([$class: 'GitSCM', branches: [[name: '*/${branch}']],
        doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [],
        userRemoteConfigs: [[credentialsId: "${git_auth}", url:
        'http://gitcebon.cebon-company.online:8080/fanyao/vue_nginx.git']]])
    }

    stage('打包，部署网站') {
        //使用NodeJS的npm进行打包
        nodejs('NodeJS'){
            sh '''
                cnpm install
                cnpm run build
            '''
    }

    //=====以下为远程调用进行项目部署========
    sshPublisher(publishers: 
        [
            // 拷贝编译后的dist文件夹下内容，到ngin的目录下
            sshPublisherDesc(configName: '192.168.99.224',
            transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '',
            execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes:false, 
            patternSeparator: '[, ]+', remoteDirectory: '/usr/share/nginx/html/vuenginx',
            remoteDirectorySDF: false, removePrefix: 'dist', sourceFiles: 'dist/**')],
            usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false),

            // 拷贝nginx的配置文件到nginx下
            sshPublisherDesc(configName: '192.168.99.224', 
            transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', 
            execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, 
            patternSeparator: '[, ]+', remoteDirectory: '/etc/nginx/conf.d', 
            remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'vue_nginx.conf')], 
            usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)

            // 拷贝部署脚本deploy.sh到nginx下，并执行脚本
            sshPublisherDesc(configName: '192.168.99.224', 
            transfers: [sshTransfer(cleanRemote: false, excludes: '', 
            execCommand: '/usr/share/nginx/html/vuenginx/deploy.sh', execTimeout: 120000, flatten: false,
            makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', 
            remoteDirectory: '/usr/share/nginx/html/vuenginx', remoteDirectorySDF: false, 
            removePrefix: '', sourceFiles: 'deploy.sh')], usePromotionTimestamp: false, 
            useWorkspaceInPromotion: false, verbose: false)

        ]
    )
    
}