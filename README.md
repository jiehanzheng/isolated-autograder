# isolated-autograder

## Setup
    # build Docker image with GCC installed
    docker build -t testing_container testing_container/

    # install Python client library to control Docker
    sudo pip install docker-py

You also need to make sure that Docker's environment variables `DOCKER_HOST` (and `DOCKER_TLS_VERIFY` and `DOCKER_CERT_PATH` if using TLS) are set and accessible when you run framework commands.

## Overview of framework
This is a very simple framework that isolates untrusted code and your system.  Student code is compiled and run inside a clean Docker container without network access, and code on your machine sets up files and scripts for the container to use.

The **input directory** should contain student code files, test datasets, and test scripts.  This directory is made available in the container to your supplied test script as `/mnt/input/`.

The **test script** will be executed as Docker container's root user.  It is responsible for running student code as framework-provided `testing` user, as well as setting up file permissions for the `testing` user.  Test scripts should redirect/write whatever output needed for performing evaluation later and providing student feedback to `/mnt/output/` in the container.  The framework ensures that `/mnt/output/` directory (used for grading) is not accessible by the `testing` user.  Finally and optionally, your test script is also responsible for limiting the time allowed for student code to run.

Whatever is in the container's `/mnt/output/` appears in your supplied **output directory**.

The **test manifest** file provides the framework with the following information:
- Which test script (in container input volume) to run, once the container is started
- How much system resource should be allocated to the container (CPU utilization, number of CPUs, etc.) [planned]

## Command-line interface
    /path/to/irun test_manifest input_directory output_directory

For easiest access in your shell, add this project's `bin/` to your PATH.
