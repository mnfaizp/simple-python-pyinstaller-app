node {
  stage('Build') {
    docker.image('python:2-alpine').inside {
      sh 'python -m py_compile sources/add2vals.py sources/calc.py'
      stash(name: 'compiled-results', includes: 'sources/*.py*')
    }
  }

  stage('Test') {
    docker.image('qnib/pytest').inside {
      try {
        sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
      } catch (e) {
        echo 'Test is failed because of $e'

        throw e
      } finally {
        junit 'test-reports/results.xml'
      }
    }
  }

  stage('Manual Approval') {
    input('Approve to Deploy?')
  }

  withEnv([
    'VOLUME=$(pwd)/sources:/src',
    'IMAGE=cdrx/pyinstaller-linux:python2'
  ]) {
    stage('Deploy') {
      try {
        dir(path: env.BUILD_ID) {
            unstash(name: 'compiled-results')
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
        }
        sleep(60)
        archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
      } catch (e) {
        throw e
      }
    }
  }
}
