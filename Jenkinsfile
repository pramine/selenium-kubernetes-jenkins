/**
 * This pipeline executes Selenium tests against Chrome and Firefox, all running in the same Pod but in separate containers
 * and in parallel
 */

podTemplate(label: 'maven-selenium', containers: [
  containerTemplate(name: 'maven-firefox', image: 'maven:3.3.9-jdk-8-alpine', ttyEnabled: true, command: 'cat', envVars: [
    containerEnvVar(key: 'HTTP_PROXY', value: 'http://64.102.255.40:80'),
    containerEnvVar(key: 'HTTPS_PROXY', value: 'https://64.102.255.40:80'),
    containerEnvVar(key: 'http_proxy', value: 'http://64.102.255.40:80'),
    containerEnvVar(key: 'https_proxy', value: 'https://64.102.255.40:80'),
  ]),
  containerTemplate(name: 'maven-chrome', image: 'maven:3.3.9-jdk-8-alpine', ttyEnabled: true, command: 'cat', envVars: [
    containerEnvVar(key: 'HTTP_PROXY', value: 'http://64.102.255.40:80'),
    containerEnvVar(key: 'HTTPS_PROXY', value: 'https://64.102.255.40:80'),
    containerEnvVar(key: 'http_proxy', value: 'http://64.102.255.40:80'),
    containerEnvVar(key: 'https_proxy', value: 'https://64.102.255.40:80'),
  ]),
  containerTemplate(name: 'selenium-hub', image: 'selenium/hub:3.4.0', envVars: [
    containerEnvVar(key: 'HTTP_PROXY', value: 'http://64.102.255.40:80'),
    containerEnvVar(key: 'HTTPS_PROXY', value: 'https://64.102.255.40:80'),
    containerEnvVar(key: 'http_proxy', value: 'http://64.102.255.40:80'),
    containerEnvVar(key: 'https_proxy', value: 'https://64.102.255.40:80'),
  ]),
  // because containers run in the same network space, we need to make sure there are no port conflicts
  // we also need to adapt the selenium images because they were designed to work with the --link option
  containerTemplate(name: 'selenium-chrome', image: 'selenium/node-chrome:3.4.0', envVars: [
    containerEnvVar(key: 'HUB_PORT_4444_TCP_ADDR', value: 'localhost'),
    containerEnvVar(key: 'HUB_PORT_4444_TCP_PORT', value: '4444'),
    containerEnvVar(key: 'DISPLAY', value: ':99.0'),
    containerEnvVar(key: 'SE_OPTS', value: '-port 5556'),
    containerEnvVar(key: 'HTTP_PROXY', value: 'http://64.102.255.40:80'),
    containerEnvVar(key: 'HTTPS_PROXY', value: 'https://64.102.255.40:80'),
    containerEnvVar(key: 'http_proxy', value: 'http://64.102.255.40:80'),
    containerEnvVar(key: 'https_proxy', value: 'https://64.102.255.40:80'),
  ]),
  containerTemplate(name: 'selenium-firefox', image: 'selenium/node-firefox:3.4.0', envVars: [
    containerEnvVar(key: 'HUB_PORT_4444_TCP_ADDR', value: 'localhost'),
    containerEnvVar(key: 'HUB_PORT_4444_TCP_PORT', value: '4444'),
    containerEnvVar(key: 'DISPLAY', value: ':98.0'),
    containerEnvVar(key: 'SE_OPTS', value: '-port 5557'),
    containerEnvVar(key: 'http_proxy', value: 'http://64.102.255.40:80'),
    containerEnvVar(key: 'https_proxy', value: 'https://64.102.255.40:80'),
  ])
  ]) {
  
  node('maven-selenium') {
    stage('Checkout') {
      git 'https://github.com/carlossg/selenium-example.git'
      parallel (
        firefox: {
          container('maven-firefox') {
            stage('Test firefox') {
              sh 'env'
              sh 'ip a'
              sh 'apk update && apk add ca-certificates && update-ca-certificates && apk add openssl && apk add wget'
              sh 'sleep 1200'
              sh 'mvn -B clean test -Dselenium.browser=firefox -Dsurefire.rerunFailingTestsCount=5 -Dsleep=0'
              sh 'sleep 120'
            }
          }
        },
        chrome: {
          container('maven-chrome') {
            stage('Test chrome') {
              sh 'env'
              sh 'ip a'
              sh 'apk update && apk add ca-certificates && update-ca-certificates && apk add openssl && apk add wget'
              sh 'sleep 1200'
              sh 'mvn -B clean test -Dselenium.browser=chrome -Dsurefire.rerunFailingTestsCount=5 -Dsleep=0'
              sh 'sleep 120'
            }
          }
        }
      )
    }

    stage('Logs') {
      containerLog('selenium-chrome')
      containerLog('selenium-firefox')
    }
  }
}
