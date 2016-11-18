#!groovy​

pipeline {
    agent label:''

    script {
        def ant(command) {
            if (isUnix()) {
                sh "ant ${command}"
            } else {
                bat "ant ${command}"
            }  
        }
    }

    stages {
        stage("Boot") {
            ant "ant -f main.xml -Dinstaller.url=http://aquarius-bg.eur.ad.sag/PDShare/cc -Dinstall.dir=`pwd`/build/cc/cli client uninstall"  
        }
    }

    postBuild {
       always {
           deleteDir 'build' // cleanup 
       }
    }
}
