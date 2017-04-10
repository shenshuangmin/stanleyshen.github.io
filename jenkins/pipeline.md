# What is pipeline

Jenkins Pipeline is a `suite` plugins which supports implementing and integrating CD pipelines into Jenkins.

It provides an extensible set of tools for `modeling` simple-to-complex delivery pipelines `as code` via pipeline DSL.

A basic pipeline can be defined in two ways:

* By enterng a script directly in jekins web UI
* By creating a `Jenkinsfile` which can be checkin to source code repository

By default, pipelines provide some global variables.

* env
  * env.PATH or env.BUILD_ID etc
* params
  * all parameters defined 
* currentBuild
  * used to discovery information, currentBuild.result  currentBuild.displayName etc
  
  

# pipeline structure
[https://jenkins.io/doc/book/pipeline/syntax/](https://jenkins.io/doc/book/pipeline/syntax/)


* Declarative Pipeline

```groovy
pipeline {
  agent any
  environment {
  }
  parameters {
    string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
  }
  triggers {
    cron('H 4/* 0 0 1-5')
  }
  tools {
    maven 'maven3'
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '1'))
  }
  stages {
  stage('step1') {
    when {
      agent any
      steps {
        echo "hello world, ${params.PERSON}"
      }
    }
   } 
   } 
  }
  post {
    success {
      echo "hello, ${params.PERSON}"
    }
  }
}
```

* Scripted Pipeline

Scripted Pipeline, like Declarative Pipeline, is built on top of the underlying Pipeline sub-system. <br />
Unlike Declarative, Scripted Pipeline is effectively a general purpose DSL built with Groovy. <br />
Most functionality provided by the Groovy language is made available to users of Scripted Pipeline, which means it can be a very expressive and flexible tool with which one can author continuous delivery pipelines.


```groovy
node('tpr') {
  environment {
    aa = 4
  }
  
  stage('Example') {
    if (env.aa == 4) {
      echo 'I only execute on the master branch'
    } else {
      echo 'I execute elsewhere'
    }
    
    //prefined step sleep
    sleep(time:20, unit:'DAYS')
  }
}

```



# pipeline steps
[https://jenkins.io/doc/pipeline/steps/](https://jenkins.io/doc/pipeline/steps/)

There are many prefined steps from plugins, we can just use them directly.


# step
https://github.com/jenkinsci/ws-cleanup-plugin/blob/master/src/main/java/hudson/plugins/ws_cleanup/WsCleanup.java

```
step([$class: 'WsCleanup', cleanWhenFailure: false, cleanWhenSuccess: false, deleteDirs: true])
```


# wrap step
[https://jenkins.io/doc/pipeline/steps/workflow-basic-steps/#code-wrap-code-general-build-wrapper](https://jenkins.io/doc/pipeline/steps/workflow-basic-steps/#code-wrap-code-general-build-wrapper)

This is a special step that allows to call build wrappers (also called "Environment Configuration" in freestyle or similar projects). <br />
Just select the wrapper to use from the dropdown list and configure it as needed. <br />
Everything inside the wrapper block is under its effect.

```
wrap([$class: 'TimestamperBuildWrapper']) {
    // some block
}

```

```
node {
    // This is the current syntax for invoking a build wrapper, naming the class.
    wrap([$class: 'AnsiColorBuildWrapper']) {
        // Just some echoes to show the ANSI color.
        stage "\u001B[31mI'm Red\u001B[0m Now not"
    }
}

```

# Best practices
[https://www.cloudbees.com/blog/top-10-best-practices-jenkins-pipeline-plugin](https://www.cloudbees.com/blog/top-10-best-practices-jenkins-pipeline-plugin)