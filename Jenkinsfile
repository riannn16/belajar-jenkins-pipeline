pipeline {
    agent none

    environment {
        AUTHOR = "Rian"
        EMAIL  = "rian16.febriansyah@gmail.com"
    }

    // triggers {
    //    cron("*/5 * * * *")
    // }

    parameters {
        string(name: "NAME", defaultValue: "Guest", description: "What is your name?")
        text(name: "DESCRIPTION", defaultValue: "Guest", description: "Tell me about you")
        booleanParam(name: "DEPLOY", defaultValue: false, description: "Need to deploy?")
        choice(name: "SOCIAL_MEDIA", choices: ['Instagram', 'Facebook', 'Twitter'], description: "Wich social media")
        password(name: "SECRET", defaultValue: "", description: "Encrypt Key")

    }

    options {
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')
    }

    stages {

        stage("Parameter") {
            agent {
                node {
                    label "linux && java17"
                }
            }
            steps {
                echo "Hello ${params.NAME}"
                echo "Your descriptions is ${params.SOCIAL_MEDIA}"
                echo "Need to deploy : ${params.DEPLOY} to deploy!"
                echo "Your secret is ${params.SECRET}"
            }
        }
            
        stage("Prepare") {

            environment {
                APP = credentials("rian_rahasia")
            }

            agent {
                node {
                    label "linux && java17"
                }
            }
            steps {
                echo("Author ${AUTHOR}")
                echo("Email ${EMAIL}")
                echo("Start Job   : ${env.JOB_NAME}")
                echo("Start Build : ${env.BUILD_NUMBER}")
                echo("Branch Name : ${env.BRANCH_NAME}")
                echo("App User     : ${APP_USR}")
                sh('echo "App Password : $APP_PSW" > "rahasia.txt"')
            }
        }

        stage("Build") {
            agent {
                node {
                    label "linux && java17"
                }
            }
            steps {
                
                script {
                    for (int i = 0; i < 10; i++) {
                        echo("Script ${i}")
                    }
                }
                echo("Start Build")
                sh("./mvnw clean compile test-compile")
                echo("Finish Build")
            }
        }

        stage("Test") {
            agent {
                node {
            label "linux && java17"
                }
            }
            steps {

                script {
                    def data = [
                        "firstName": "Rian",
                        "lastName" : "Febriansyah"
                    ]
                    writeJSON(file:"data.json", json: data)
                }

                echo("Start Test")
                sh("./mvnw test")
                echo("Finish Test")
            }
        }

        stage("Deploy") {
            input {
                message "Can we deploy ?"
                ok "Yes of course"
                submitter "Rian"
                parameters {
                    choice(name: "TARGET_ENV", choices: ['DEV', 'QA', 'PROD'], description: "Which Environment ?")
                }
            }
            agent {
                node {
            label "linux && java17"
                }
            }
            steps {
                echo("Deploy to ${TARGET_ENV}")
            }
        }

        stage("Release") {
            when {
                expression {
                    return params.DEPLOY
                }
            }
            agent {
                node {
            label "linux && java17"
                }
            }
            steps {
                echo("Release it")
            }
        }

    }

    post {
        always {
            echo "I will always say Hello again!"
        }
        success {
            echo "Yay, success"
        }
        failure {
            echo "Oh no, failure"
        }
        cleanup {
            echo " Don't care success or error"
        }
    }
}