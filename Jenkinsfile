pipeline{
    parameters{
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Do you want to apply the generated plan?')
    }

    environment{
        AWS_Access_Key_ID = credentials('AWS_Access_Key_ID')
        AWS_Secret_Access_Key = credentials('AWS_Secret_Access_Key')
    }

    agent any
    stages{
        stage('Checkout') {
            steps{
                script{
                    dir('Terraform'){
                        git branch: 'main', url: 'https://github.com/fazle-mubin/Terra-EC2-Jenkins.git'
                    }
                }
            }
        }

        stage('Plan'){
            steps{
                sh 'cmd /c echo %CD%'
                sh 'cmd /c cd Terraform/Terraform-files'
                sh 'cmd /c terraform.exe init'
                sh 'cmd /c terraform.exe plan -out tfplan'
                sh 'cmd /c terraform.exe show -no-color tfplan > tfplan.txt'
            }
        }

        stage('Plan Approval'){
            when{
                not{
                    equals expected: true, actual: params.autoApprove
                }
            }

            steps{
                script {
                    def plan = readFile 'Terraform/tfplan.txt'
                    input message: "Apply the plan or not?"
                    parameters: [text(name:'Plan', description: 'Please review the plan', defaultValue: plan)]
                }
            }
        }

        stage("Apply"){
            steps{
                sh 'cd Terraform/ ; terraform apply -input=false tfplan'
            }
        }
    }
}