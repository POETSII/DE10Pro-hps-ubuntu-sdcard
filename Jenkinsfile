pipeline {
	agent { label 'imagebuilder' }
	options { buildDiscarder(logRotator(numToKeepStr: '1')) }
	environment {
		CHERI = 'beri'
		MULTI = '1'
	}
	stages {
		stage('Pull artifacts') {
			steps {
//				copyArtifacts filter: '**', fingerprintArtifacts: true, projectName: 'CPU1-DE10-multi-synth/master'
//				copyArtifacts filter: '**', fingerprintArtifacts: true, projectName: 'thunderclap-qemu/master'
				copyArtifacts filter: 'cheri/boards/terasic_de10pro_sx/output_files/DE10_Pro.sof', fingerprintArtifacts: true, projectName: "CPU1-DE10-multi-synth/cheri=${env.CHERI},cheri_dimm=1GB,dcache=writethrough,invalidate=push,label=bionic,multi=${env.MULTI}/", selector: lastSuccessful()
			}
		}

		stage ("Building") {
			steps {
				println "Building ${env.CHERI} with ${env.MULTI} cores" 
				sh '''#!/bin/bash
				      IMAGEFILE="s10-${CHERI}-multi${MULTI}-sd.img.xz"
                                      rm -f $IMAGEFILE || true
				      #source $SOCEDS_DEST_ROOT/env.sh && 
				      ./build_sdcard.sh intel-a10soc-devkit
				      mv sdimage.img.xz $IMAGEFILE
				'''
			}
		}
/*
		stage ('Build Enclustra AA1/PE1') {
			steps {
				sh '''#!/bin/bash
                                      rm -f enclustra-sd.img.xz || true
				      source $SOCEDS_DEST_ROOT/env.sh && ./build_sdcard.sh enclustra-mercury-aa1-pe1
				      mv sdimage.img.xz enclustra-sd.img.xz
				'''
			}
		}
*/

	}
	post {
		always {
			archiveArtifacts artifacts: "*-sd.img.xz,cheri/boards/terasic_de10pro_sx/output_files/*.rbf,cheri/boards/terasic_de10pro_sx/output_files/*-hps.sof", fingerprint:true 
		}
	}
}
