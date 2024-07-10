 // Special thanks to Frazer Seymour for inspiration for a lot of this Jenkinsfil

// @Library(['PSL@master']) _

// common_scm = new ors.utils.common_scm(steps, env)
// common_ci = new ors.ci.common_ci(steps, env, docker)
// common_messaging = new ors.utils.CommonMessaging(steps, env)

// def changeset = null;

// def REGISTRY = "autodesk-docker.art-bobcat.autodesk.com";
// def CREDENTIALS_TO_USE = "local-svc_p_ors_art"
// def IMAGE_NAME = "ubuntu-focal-araas-base";
// def IMAGE_TAG = "latest";
// def IMAGE_FULL = "${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}";

// def BUILT_IMAGE = "false";

// def IMAGE_NAME_WINDOWS_ARAAS_BASE = "windows-araas-base";
// def IMAGE_FULL_WINDOWS_ARAAS_BASE = "${REGISTRY}/${IMAGE_NAME_WINDOWS_ARAAS_BASE}:${IMAGE_TAG}";

// def IMAGE_NAME_WINDOWS_PYARAAS = "windows-pyaraas";
// def IMAGE_FULL_WINDOWS_PYARAAS = "${REGISTRY}/${IMAGE_NAME_WINDOWS_PYARAAS}:${IMAGE_TAG}";

// def IMAGE_NAME_WINDOWS_PYATK = "windows-pyatk";
// def IMAGE_FULL_WINDOWS_PYATK = "${REGISTRY}/${IMAGE_NAME_WINDOWS_PYATK}:${IMAGE_TAG}";


// def BUILT_IMAGE_WINDOWS = "false";

// def GITHUB_REPO = 'mint/araas'; 
// def GITHUB_API_URL = "https://git.autodesk.com/api/v3/repos/${GITHUB_REPO}";
// def ISSUES_URL = "https://git.autodesk.com/${GITHUB_REPO}/issues/492"; 

pipeline {
  agent any

  stages {
    // HARMONY SCAN 
    stage('Harmony Scan') {
      when {
        anyOf {
          branch 'main'
        }
      }
      
      steps {
        catchError(message: 'Failed to get Harmony', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
          script {
            echo "Starting Harmony Scan"
          }
        }
      }
    }
  
    stage("Get Changeset") {
      steps {
        catchError(message: 'Change set failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
          script {
            echo "Changeset"
          }
        }
      }
    }
    
    stage("Base Image LINUX") {
      when {
        // To skip this stage turn to false in jenkins_trigger.yaml file 
        expression { cicd_config.platforms.linux.base_image }
      }
      parallel {
        stage("[PR] ubuntu-focal-araas-base") {
          when {
            allOf {
              expression { return cicd_config.platforms.linux.build_types.pr_base && (changeset.includes('infra/dependencies/.*') || changeset.includes('infra/containers/ubuntu-focal-araas-base/.*'))}
            }
          }
          stages {
            stage("Build ARaaS Base Image") {
              steps {
                catchError(message: '[PR] ubuntu-focal-araas-base failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') { 
                  script {   
                    echo "Building ARaaS Base Image"
                  } 
                }
              }                
            }
          }
        }

        stage("[Main] ubuntu-focal-araas-base") {
          when {
            allOf {
              branch 'main'
              expression { cicd_config.platforms.linux.build_types.main_base }
            }
          }
          stages {
            stage("Build + Push ARaaS Base Image") {
              steps {
                catchError(message: '[Main] ubuntu-focal-araas-base failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    echo "Building ARaaS Base Image"
                  }
                }
              }   
            }
          }  
        }
      }
    }

    stage("Build + Test + Linux") {
      when {
        anyOf {
          branch 'main'
          changeRequest target: 'main'
          // This stage is false at default in jenkins_trigger.yaml file 
          expression { cicd_config.platforms.linux.run_test.main_test }
        }
      }
      parallel {
        stage("Build + Test ubuntu-focal-pyatk") {
          when {
            anyOf {
              branch 'main'
              expression { return cicd_config.platforms.linux.run_test.build_pyatk && (changeset.includes('src/pyatk/.*') || changeset.includes('src/robot_server/.*') || changeset.includes('src/common/.*') ||  changeset.includes('src/third_party/.*') || changeset.includes('cmake/.*'))}
            }
          }
          stages {
            stage("Prepare") {
              steps {
                catchError(message: 'Prepare Build + Test ubuntu-focal-pyatk failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    echo "Preparing Build + Test ubuntu-focal-pyatk"
                  }
                }
              }
            }
            stage("Build Container (ubuntu-focal-pyatk)") {
              steps {
                catchError(message: 'Build Container (ubuntu-focal-pyatk) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  
                    script {
                      echo "Building Container (ubuntu-focal-pyatk)"
                    }
                  
                }
              }
            }

            stage("Run Tests (ubuntu-focal-pyatk)") {
              steps {
                catchError(message: 'Run Tests (ubuntu-focal-pyatk) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  retry(3){
                    timeout(time: 15, unit: 'MINUTES') {
                     echo "Running Tests (ubuntu-focal-pyatk)"
                    }
                  }
                }
              }
            }
          }
        }

        stage("Build + Test ubuntu-focal-pyaraas") {
          when {
            anyOf {
              branch 'main'
              expression { cicd_config.platforms.linux.run_test.build_pyaraas && (changeset.includes('src/pyatk/.*') || changeset.includes('src/pyaraas/.*') || changeset.includes('src/robot_server/.*') || changeset.includes('src/common/.*') ||  changeset.includes('src/third_party/.*') || changeset.includes('cmake/.*'))}
            }
          }
          stages {
            stage("Prepare") {
              steps {
                catchError(message: 'Prepare Build + Test ubuntu-focal-pyaraas failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    echo "Preparing Build + Test ubuntu-focal-pyaraas"
                  }
                }
              }
            }
            stage("Build pyatk Container (ubuntu-focal-pyatk)") {
              steps {
                catchError(message: 'Build pyatk Container (ubuntu-focal-pyatk) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  
                    script {
                      echo "Building pyatk Container (ubuntu-focal-pyatk)"
                    }
                  
                }
              }
            }
            stage("Build Container (ubuntu-focal-pyaraas)") {
              steps {
                catchError(message: 'Build Container (ubuntu-focal-pyaraas) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  
                    script {
                      echo "Building Container (ubuntu-focal-pyaraas)"
                    }
                  
                }
              }
            }

            stage("Run Tests (ubuntu-focal-pyaraas)") {
              steps {
                catchError(message: 'Run Tests (ubuntu-focal-pyaraas) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  retry(3){
                    timeout(time: 15, unit: 'MINUTES') {
                      echo "Running Tests (ubuntu-focal-pyaraas)"
                    }
                  }
                }
              }
            }
          }
        }  
      }
    }


    stage("Base Image WINDOWS") {
      when {
        // To skip this stage turn to false in jenkins_trigger.yaml file 
        expression { cicd_config.platforms.windows.base_image }
      }

      parallel {
        stage("[PR] windows-araas-base") {
          // agent {
          //   label 'aws-win2019'
          // }
          when {
            expression { return cicd_config.platforms.windows.build_types.pr_base || (changeset.includes('infra/dependencies/.*') || changeset.includes('infra/containers/windows-araas-base/.*')) }
          }
          stages {
            stage("Build ARaaS Base Image Windows") { 
              steps {
                catchError(message: '[PR] windows-araas-base failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    echo "Building ARaaS Base Image Windows"
                  }
                }
              }   
            }
          }
        }


        stage("[Main] windows-araas-base") {
          // agent {
          //   label 'aws-win2019'
          // }
          when {
            allOf {
              branch 'main'
              expression { cicd_config.platforms.windows.build_types.main_base }
            }
          }
          stages {
            stage("Build + Push ARaaS Base Image Windows") {
              steps {
                catchError(message: '[[Main] windows-araas-base failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    echo "Building + Push ARaaS Base Image Windows"
                  }
                }
              }  
            }
          }
        }
      }
    }

    stage("Build + Test + Windows") {
      when {
        anyOf {
          branch 'main'
          // To skip this stage turn to false in trigger.yaml file 
          expression { cicd_config.platforms.windows.run_test.main_test }
        }
      } 
      parallel {
        stage("Build + Test windows-pyatk") {
          // agent {
          //   label 'aws-win2019'
          // }
          when {
            anyOf {
              branch 'main'
              expression { return cicd_config.platforms.windows.run_test.build_pyatk || (changeset.includes('src/pyatk/.*') || changeset.includes('src/robot_server/.*') || changeset.includes('src/common/.*') ||  changeset.includes('src/third_party/.*') || changeset.includes('cmake/.*'))}
            }
          }
          stages {
            stage("Prepare"){
              steps {
                echo "Preparing Build + Test windows-pyatk"
              }
            }
            stage("Build Container (windows-pyatk)") {
              steps {
                catchError(message: 'Prepare Build Container (windows-pyatk) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  
                    
                    script {
                      echo "Building Container (windows-pyatk)"
                    }
                  
                }
              }
            }

            // opencv-python doesn't properly install on docker container 2019
            // stage("Run Tests (windows-pyatk)") {
            //   steps {
            //     bat 'docker run --rm --network=nat windows-pyatk:latest .\\run_pyatk_win_test.bat'
            //   }
            // }
          }
        }

        stage("Build + Test windows-pyaraas") {
          // agent {
          //   label 'aws-win2019'
          // }
          when {
            anyOf {
              branch 'main'
              expression { return cicd_config.platforms.windows.run_test.build_pyaraas || (changeset.includes('src/pyatk/.*') || changeset.includes('src/pyaraas/.*') || changeset.includes('src/robot_server/.*') || changeset.includes('src/common/.*') ||  changeset.includes('src/third_party/.*') || changeset.includes('cmake/.*'))}
            }
          }
          stages {
            stage("Prepare") {
              steps {
                echo "Preparing Build + Test windows-pyaraas"
              }
            }
            stage("Build pyatk Container (windows-pyatk)") {
              steps {
                catchError(message: 'Prepare Build Container (windows-pyatk) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  
                    script {
                      echo "Building Container (windows-pyatk)"
                    }
                  
                }
              }
            }
            stage("Build Container (windows-pyaraas)") {
              steps {
                catchError(message: 'Build Container (windows-pyaraas) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  
                    script {
                      echo "Building Container (windows-pyaraas)"
                    }
                  
                }
              }
            }
            // opencv-python doesn't properly install on docker container 2019
            // stage("Run Tests (windows-pyaraas)") {
            //   steps {
            //     bat 'docker run --rm --network=nat autodesk-docker.art-bobcat.autodesk.com/windows-pyaraas:latest nmake -f .\\Makefile.win test'
            //   }
            // }
          }
        }
      }
    }


    stage("Build + Test + Robot Server") {
      when {
        anyOf {
          branch 'main'
          // To skip this stage turn to false in jenkins_trigger.yaml file 
          expression { cicd_config.platforms.macOs.run_test.main_test }
        }
      } 
      parallel {
        stage("Build + Test ubuntu-focal-robot-server") {
          when {
            anyOf {
              branch 'main'
              expression { return cicd_config.platforms.macOs.run_test.build_server && (changeset.includes('src/robot_server/.*') || changeset.includes('src/common/.*') ||  changeset.includes('src/third_party/.*') || changeset.includes('cmake/.*'))}
            }
          }
          stages {
            stage("Prepare") {
              steps {
                catchError(message: ' Prepare Build + Test ubuntu-focal-robot-server failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    echo "Preparing Build + Test ubuntu-focal-robot-server"
                  }
                }
              }
            }
            stage("Build Container (ubuntu-focal-robot-server)") {
              steps {
                catchError(message: ' Build Container (ubuntu-focal-robot-server) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  
                    script {
                      echo "Building Container (ubuntu-focal-robot-server)"
                    }
                  
                }
              }
            }
            // Currently no robot server tests
          }
        }
      }
    }


    stage("Release Linux") {
      when {
        // To skip this stage turn to false in jenkins_trigger.yaml file 
        expression { cicd_config.platforms.linux.release }
      }
      parallel {
        stage("Pyatk Ubuntu") {
          when { buildingTag() }
          stages {
            stage("Prepare") {
              steps {
                script {
                  sh "docker pull ${IMAGE_FULL}"
                  sh "docker tag autodesk-docker.art-bobcat.autodesk.com/ubuntu-focal-araas-base:latest ubuntu-focal-araas-base:latest"
                }
              }
            }
            stage("Prepare Run Release Tests (ubuntu-focal-pyatk)") {
              steps {
                catchError(message: ' Run Release Tests (ubuntu-focal-pyatk)', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    common_ci.app_package_and_publish(
                      docker_credentials: "${CREDENTIALS_TO_USE}",
                      dockerfile_path: "infra/containers/ubuntu-focal-pyatk/Dockerfile",
                      docker_build_args: "--network=host --pull=false",
                      docker_registry: "https://${REGISTRY}/",
                      deployable_image: "ubuntu-focal-pyatk",
                      tag: common_ci.commit_hash(),
                      update_latest: "true"
                    )
                  }
                }
                sh 'docker run --rm --network=host autodesk-docker.art-bobcat.autodesk.com/ubuntu-focal-pyatk:latest /bin/bash -c "(cd /root/araas/release/tests && ./run_integration_tests.sh)"'
              }
            }
            stage ('Push PyPi Wheel') {
              steps {
                catchError(message: ' Push PyPi Wheel failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'jenkins_service_acct_ads', usernameVariable: 'ADS_USERNAME', passwordVariable: 'ADS_PASS']]) {
                        sh 'docker run --rm -e ADS_USERNAME -e ADS_PASS --network=host autodesk-docker.art-bobcat.autodesk.com/ubuntu-focal-pyatk:latest /bin/bash -c "(cd /root/araas/release/artifacts && bash pyatk_wheel_linux.sh)"'
                    }
                  }
                }
              }
            }
          }
        }
        stage("Pyaraas Ubuntu") {
          when { buildingTag() }
          stages {
            stage("Prepare") {
              steps {
                script {
                  sh "docker pull ${IMAGE_FULL}"
                  sh "docker tag autodesk-docker.art-bobcat.autodesk.com/ubuntu-focal-araas-base:latest ubuntu-focal-araas-base:latest"
                }
              }
            }
            stage("Build pyatk (ubuntu-focal-pyatk)") {
              steps {
                catchError(message: ' Prepare Build pyatk (ubuntu-focal-pyatk) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    common_ci.app_package(
                      docker_credentials: "${CREDENTIALS_TO_USE}",
                      dockerfile_path: "infra/containers/ubuntu-focal-pyatk/Dockerfile",
                      docker_build_args: "--network=host --pull=false",
                      docker_registry: "https://${REGISTRY}/",
                      deployable_image: "ubuntu-focal-pyatk",
                      tag: common_ci.commit_hash(),
                      update_latest: "true"
                    )
                  }
                }
              }
            }
            stage("Run Release Tests (ubuntu-focal-pyaraas)") {
              steps {
                catchError(message: ' Run Release Tests (ubuntu-focal-pyaraas) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    common_ci.app_package_and_publish(
                      docker_credentials: "${CREDENTIALS_TO_USE}",
                      dockerfile_path: "infra/containers/ubuntu-focal-pyaraas/Dockerfile",
                      docker_build_args: "--network=host --pull=false",
                      docker_registry: "https://${REGISTRY}/",
                      deployable_image: "ubuntu-focal-pyaraas",
                      tag: common_ci.commit_hash(),
                      update_latest: "true"
                    )
                  }
                }
                sh 'docker run --rm --network=host autodesk-docker.art-bobcat.autodesk.com/ubuntu-focal-pyaraas:latest /bin/bash -c "(cd /root/araas/release/tests && ./run_integration_tests.sh)"'
              }
            }
            stage ('Push PyPi Wheel') {
              steps {
                catchError(message: ' Push PyPi Wheel failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'jenkins_service_acct_ads', usernameVariable: 'ADS_USERNAME', passwordVariable: 'ADS_PASS']]) {
                        sh 'docker run --rm -e ADS_USERNAME -e ADS_PASS --network=host autodesk-docker.art-bobcat.autodesk.com/ubuntu-focal-pyaraas:latest /bin/bash -c "(cd /root/araas/release/artifacts && bash pyaraas_wheel_linux.sh)"'
                    }
                  }
                }
              }
            }
          }
        }
      }
    }


    stage("Release Windows") {
      when {
        //To skip this stage turn to false in jenkins_trigger.yaml file 
        expression { cicd_config.platforms.windows.release }
      }
      parallel {

        stage("Pyatk Windows") {
          agent {
            label 'aws-win2019'
          }
          when { buildingTag() }
          stages {
            stage("Prepare"){
              steps {
                bat "docker pull ${IMAGE_FULL_WINDOWS_ARAAS_BASE}"
                bat "docker tag autodesk-docker.art-bobcat.autodesk.com/windows-araas-base:latest windows-araas-base:latest"
              }
            }
            stage("Run Release Tests (windows-pyatk)") {
              steps {
                catchError(message: ' Prepare Run Release Tests (windows-pyatk)failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    common_ci.app_package_and_publish(
                      docker_credentials: "${CREDENTIALS_TO_USE}",
                      dockerfile_path: "infra/containers/windows-pyatk/Dockerfile",
                      docker_build_args: "--network=nat --pull=false",
                      docker_registry: "https://${REGISTRY}/",
                      deployable_image: "windows-pyatk",
                      tag: common_ci.commit_hash(),
                      update_latest: "true"
                    )
                  }
                }
                // opencv-python doesn't properly install on docker container 2019
                // bat 'docker run --rm --network=nat windows-pyatk:latest .\\run_pyatk_win_test.bat'
              }
            }

            stage('Push PyPi Wheel') {
              steps {
                catchError(message: ' Push PyPi Wheel failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    bat "docker pull ${IMAGE_FULL_WINDOWS_PYARAAS}"
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'jenkins_service_acct_ads', usernameVariable: 'ADS_USERNAME', passwordVariable: 'ADS_PASS']]) {
                      bat 'docker run --rm -e ADS_USERNAME="%ADS_USERNAME%" -e ADS_PASS="%ADS_PASS%" --network=nat autodesk-docker.art-bobcat.autodesk.com/windows-pyatk:latest ..\\..\\release\\artifacts\\pyatk_wheel_windows.bat'
                    }
                  }
                }
              }
            }
          }
        }

        stage("Pyaraas Windows") {
          agent {
            label 'aws-win2019'
          }
          when { buildingTag() }
          stages {
            stage("Prepare"){
              steps {
                bat "docker pull ${IMAGE_FULL_WINDOWS_ARAAS_BASE}"
                bat "docker tag autodesk-docker.art-bobcat.autodesk.com/windows-araas-base:latest windows-araas-base:latest"
              }
            }
            stage("Build pyatk (windows-pyatk)") {
              steps {
                catchError(message: ' Build pyatk (windows-pyatk) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    common_ci.app_package(
                      docker_credentials: "${CREDENTIALS_TO_USE}",
                      dockerfile_path: "infra/containers/windows-pyatk/Dockerfile",
                      docker_build_args: "--network=nat --pull=false",
                      docker_registry: "https://${REGISTRY}/",
                      deployable_image: "windows-pyatk",
                      tag: common_ci.commit_hash(),
                      update_latest: "true"
                    )
                  }
                }
              }
            }
            stage("Run Release Tests (windows-pyaraas)") {
              steps {
                catchError(message: ' Run Release Tests (windows-pyaraas) failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    common_ci.app_package_and_publish(
                      docker_credentials: "${CREDENTIALS_TO_USE}",
                      dockerfile_path: "infra/containers/windows-pyaraas/Dockerfile",
                      docker_build_args: "--network=nat --pull=false",
                      docker_registry: "https://${REGISTRY}/",
                      deployable_image: "windows-pyaraas",
                      tag: common_ci.commit_hash(),
                      update_latest: "true"
                    )
                  }
                }
              // opencv-python doesn't properly install on docker container 2019
              // bat 'docker run --rm --network=nat autodesk-docker.art-bobcat.autodesk.com/windows-pyaraas:latest nmake -f .\\Makefile.win test'
              }
            }
            stage('Push PyPi Wheel') {
              steps {
                catchError(message: ' Push PyPi Wheel failed', buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                  script {
                    bat "docker pull ${IMAGE_FULL_WINDOWS_PYARAAS}"
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'jenkins_service_acct_ads', usernameVariable: 'ADS_USERNAME', passwordVariable: 'ADS_PASS']]) {
                      bat 'docker run --rm -e ADS_USERNAME="%ADS_USERNAME%" -e ADS_PASS="%ADS_PASS%" --network=nat autodesk-docker.art-bobcat.autodesk.com/windows-pyaraas:latest ..\\..\\release\\artifacts\\pyaraas_wheel_windows.bat'
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
