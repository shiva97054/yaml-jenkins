// This Jenkinsfile deploys a single YAML file to a Kubernetes cluster.

pipeline {
    // Defines where the pipeline will run. 'any' means any available agent.
    // For production, you might specify a label like: agent { label 'kubernetes-agent' }
    agent any

   // Define environment variables if needed, e.g., for target namespace.

environment {
    APP_NAME = 'my-web-app' // Example variable
    // KUBE_NAMESPACE = 'dev' // Example variable, uncomment if needed
}

    stages {
        // --- Stage 1: Checkout Source Code ---
        // Retrieves the code from the Git repository.
        stage('Checkout Code') {
            steps {
                // 'checkout scm' automatically clones the repo configured in the Jenkins job.
                checkout scm
                echo "Source code checked out."
            }
        }

        // --- Stage 2: Deploy YAML to Kubernetes ---
        // This is the core stage where the YAML file is applied.
        
stage('Deploy to Kubernetes') {
    steps {
        script {
            sh 'kubectl apply -f my-app-deployment.yaml --validate=false'
        }
    }
}
                    // **IMPORTANT:** The Jenkins agent running this pipeline must have
                    // `kubectl` installed and configured to connect to your Kubernetes cluster.
                    // This typically means having a `kubeconfig` file in the correct location
                    // or using Jenkins credentials.

                    // Option 1 (Simple: kubectl configured on agent)
                    // This command applies the YAML file.
                    // If you're using a specific namespace:
                    // sh "kubectl apply -f my-app-deployment.yaml -n ${env.KUBE_NAMESPACE}"
                    sh 'kubectl apply -f my-app-deployment.yaml'
                    echo "Applied my-app-deployment.yaml to Kubernetes."

                    // Option 2 (Recommended: Using Jenkins Kubeconfig Credentials)
                    // Requires the 'Kubernetes CLI' plugin in Jenkins.
                    // You would have configured a 'Kubeconfig' credential in Jenkins
                    // with the ID 'your-kubeconfig-credential-id'.
                    /*
                    withKubeConfig(credentialsId: 'your-kubeconfig-credential-id') {
                        // If you need to specify a context or namespace from the kubeconfig:
                        // sh "kubectl --context=my-cluster-context --namespace=${env.KUBE_NAMESPACE} apply -f my-app-deployment.yaml"
                        sh 'kubectl apply -f my-app-deployment.yaml'
                        echo "Applied my-app-deployment.yaml using Jenkins credentials."
                    }
                    */

                    // Option 3 (For OpenShift using OpenShift Pipeline Plugin)
                    // Requires the 'OpenShift Pipeline' plugin in Jenkins.
                    // You would configure OpenShift credentials (e.g., token) in Jenkins.
                    /*
                    openshift.withProject('your-openshift-project') {
                        openshift.apply(file: 'my-app-deployment.yaml')
                        echo "Applied my-app-deployment.yaml to OpenShift project."
                    }
                    */
                }
            }
        }

        // --- Stage 3: Verify Deployment (Optional but Recommended) ---
        // Checks the status of the deployed application.
        stage('Verify Deployment') {
            steps {
                script {
                    echo "Waiting for deployment 'my-simple-app' to be ready..."
                    // Wait for the deployment rollout to complete.
                    sh 'kubectl rollout status deployment/my-simple-app --timeout=5m'
                    echo "Deployment 'my-simple-app' is ready!"
                    // Show basic info about the deployed resources.
                    sh 'kubectl get pods -l app=my-simple-app'
                    sh 'kubectl get svc my-simple-app-service'
                }
            }
        }
    }

    // --- Post-build Actions ---
    // Actions that run after the pipeline completes, regardless of success or failure.
    post {
        always {
            cleanWs() // Clean up the Jenkins workspace to save disk space
            echo 'Deployment pipeline finished.'
        }
        success {
            echo 'YAML file deployed successfully! ✅'
        }
        failure {
            echo 'Deployment failed. Check console output for details. ❌'
        }
    }
}
