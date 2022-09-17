node {
  stage('Build') {
    docker.image('python:2-alpine').inside {
      sh 'python -m py_compile sources/add2vals.py sources/calc.py'
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

  stage('Deliver') {
    docker.image('cdrx/pyinstaller-linux:python2').inside("--entrypoint=''") {
      try {
        sh 'git push https://git.heroku.com/python-app-devops.git'
      } catch (e)  {
        echo 'Build failed because ${e}'

        throw e
      }
    }
  }
}
