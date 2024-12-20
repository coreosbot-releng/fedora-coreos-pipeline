node {
    checkout scm
    // these are script global vars
    pipeutils = load("utils.groovy")
    pipecfg = pipeutils.load_pipecfg()
}

properties([
    pipelineTriggers([
        // run every 24h at 2am UTC
        cron("0 2 * * *")
    ]),
    buildDiscarder(logRotator(
        numToKeepStr: '100',
        artifactNumToKeepStr: '100'
    )),
    durabilityHint('PERFORMANCE_OPTIMIZED')
])

node {
    def mechanical_streams = pipeutils.streams_of_type(pipecfg, 'mechanical')
    def scheduled_streams = pipeutils.scheduled_streams(pipecfg, mechanical_streams)
    if (scheduled_streams) {
        mechanical_streams = scheduled_streams
    }

    mechanical_streams.each{
        try {
            echo "Triggering build for mechanical stream: ${it}"
            build job: 'build', wait: true, parameters: [
              string(name: 'STREAM', value: it),
              booleanParam(name: 'EARLY_ARCH_JOBS', value: false)
            ]
        } catch (e) {
            echo "Build failed for mechanical stream: ${it}"
        }   
    }
}
