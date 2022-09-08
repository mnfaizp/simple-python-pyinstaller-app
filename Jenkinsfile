node {
  docker.image('python:2-alpine').inside {
    stage('Build') {
      sh 'python -m py_compile sources/add2vals.py sources/calc.py'
    }
  }

  docker.image('qnib/pytest').inside {
    try {
      stage('Test') {
        sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
      }
    } catch (e) {
      echo 'Test is failed because of $e'

      throw e
    } finally {
      junit 'test-reports/results.xml'
    }
  }
}
