.. _reporter_over:

Overview
========

Reporter is a package for collecting and generating test reports in YAML
format. This results file contains all the details about execution
(section hierarchy, time, results, etc.)

The *results.json* report contains hierarchical information about the pyATS job
executed. The top level is the ``TestSuite`` which contains information about
the job as a whole. Under the ``TestSuite`` are all of the ``Task``'s that were
executed as a part of the job. Each ``Task`` then has the various sections of
testing underneath for ``CommonSetup``, ``CommonCleanup``, and ``Testcase``'s.
These then have child sections which can be ``TestSection``, ``SetupSection``,
``CleanupSection``, and ``Subsection``. The children of these would be ``Step``,
which can be nested with their own children ``Step``'s.

Each level of the report contains different information relevent to that
section. This table shows the possible fields for each section of the report.
Any information that falls outside of these fields will be included in the
``extra`` mapping.

.. code-block:: text

    +---------------+----------------------------------------------------------+
    | Section       | Field         | Description                              |
    +===============+===============+==========================================+
    | TestSuite     | type          | Identifier that this section is the root |
    |               |               |  TestSuite                               |
    |               | id            | Unique ID for this job execution         |
    |               | name          | Name from jobfile                        |
    |               | starttime     | Timestamp when job execution began       |
    |               | stoptime      | Timestamp when job execution ended       |
    |               | runtime       | Duration of execution                    |
    |               | cli           | Command that started Easypy              |
    |               | jobfile       | Location of jobfile                      |
    |               | pyatspath     | Python environment executing pyATS       |
    |               | pyatsversion  | Version of pyATS installed               |
    |               | host          | Name of host machine                     |
    |               | submitter     | User that started execution              |
    |               | archivefile   | Path to generated archive file           |
    |               | summary       | Combined summary of all Tasks            |
    |               | details       | Details about any exceptions or errors   |
    |               | extra         | Map of extra info about the TestSuite    |
    |               | tasks         | List of child Tasks                      |
    +---------------+---------------+------------------------------------------+
    | Task          | type          | Identifier that this section is a Task   |
    |               | id            | Unique ID for this Task                  |
    |               | name          | Name of TestScript                       |
    |               | starttime     | Timestamp when execution began           |
    |               | stoptime      | Timestamp when execution ended           |
    |               | runtime       | Duration of execution                    |
    |               | description   | Description of TestScript                |
    |               | logfile       | Path to logfile for this Task            |
    |               | testscript    | Path to testscript                       |
    |               | parameters    | Any parameters passed to this Task       |
    |               | summary       | Summary of results                       |
    |               | details       | Details about any exceptions or errors   |
    |               | extra         | Map of extra info about the Task         |
    |               | sections      | List of child Sections                   |
    +---------------+---------------+------------------------------------------+
    | Section       | type          | Specific type of section being           |
    |               |               |  represented
    |               | id            | Unique ID for this Section               |
    |               | name          | Name of this Section                     |
    |               | starttime     | Timestamp when this Section began        |
    |               | stoptime      | Timestamp when this Section ended        |
    |               | runtime       | Duration of execution                    |
    |               | description   | Description of this Section              |
    |               | xref          | XReference to the code defining this     |
    |               |               |  Section                                 |
    |               | logs          | Path to logfile showing execution of     |
    |               |               |  this Section, as well as the beginning  |
    |               |               |  byte and size in bytes                  |
    |               | parameters    | Any parameters passed to this Section    |
    |               | processors    | Lists of processors that ran for this    |
    |               |               |  section, both before and after          |
    |               | result        | The test result of this Section          |
    |               | details       | Details about any exceptions or errors   |
    |               | extra         | Map of extra info about this Section     |
    |               | sections      | Any child sections of this section       |
    |               |               |  (Testcases have TestSections, which can |
    |               |               |   have Steps, etc.)                      |
    +---------------+---------------+------------------------------------------+

Everything under ``TestScript`` is considered a ``Section`` as the information
gathered is largely the same. The minimal uniqe data (such as ``index`` for
``Step``) is kept under extra. The ``type`` denotes what the type each
``Section`` is.
A sample *results.yaml* file looks like this:

.. code-block:: yaml

    version: '2'
    report:
      type: TestSuite
      id: example_job.2019Sep19_19:56:06.569499
      name: example_job
      starttime: 2019-09-19 19:56:07.603283
      stoptime: 2019-09-19 19:56:19.951458
      runtime: 12.35
      cli: pyats run job job/example_job.py --testbed-file etc/example_testbed.yaml
        --no-mail
      pyatspath: /Users/user/env
      pyatsversion: '19.11'
      host: HOSTNAME
      submitter: user
      archivefile: /Users/user/env/users/user/archive/19-09/example_job.2019Sep19_19:56:06.569499.zip
      summary:
        passed: 13
        passx: 0
        failed: 1
        errored: 12
        aborted: 0
        blocked: 4
        skipped: 0
        total: 30
        success_rate: 43.33
      extra:
        testbed: example_testbed
      tasks:
        - type: Task
          id: Task-1
          name: base_example
          starttime: 2019-09-19 19:56:08.432390
          stoptime: 2019-09-19 19:56:08.617640
          runtime: 0.19
          description: |+
            base_example.py

            This is a comprehensive example base script that walks users through AEtest
            infrastructure features, what they are for, how they are used, how it impacts
            their testing, etc.

          logfile: TaskLog.Task-1
          testscript: /Users/user/examples/comprehensive/base_example.py
          parameters:
            labels: {}
            links: []
            parameter_A: jobfile value A
            routers: []
            testbed: <pyats.topology.testbed.Testbed object at 0x106da92b0>
            tgns: []
          summary:
            passed: 3
            passx: 0
            failed: 0
            errored: 3
            aborted: 0
            blocked: 0
            skipped: 0
            total: 6
            success_rate: 50.0
          sections:
            - type: CommonSetup
              id: common_setup
              name: common_setup
              starttime: 2019-09-19 19:56:08.434411
              stoptime: 2019-09-19 19:56:08.458939
              runtime: 0.02
              description: |+
                Common Setup Section

                    This is the docstring for your common setup section. Users should document
                    the number of common setup subsections so that by reading this block of
                    comments, it gives a generic feeling as to how CommonSetup is built and run.

              xref:
                file: /Users/user/examples/comprehensive/base_example.py
                line: 191
              logs:
                begin: 0
                file: TaskLog.Task-1
                size: 4317
              parameters:
                labels: {}
                links: []
                parameter_A: jobfile value A
                parameter_B: value B
                routers: []
                testbed: <pyats.topology.testbed.Testbed object at 0x105ea92b0>
                tgns: []
              result:
                value: passed
              sections:
                - type: Subsection
                  id: a_simple_subsection
                  name: a_simple_subsection
                  starttime: 2019-09-19 19:56:08.435632
                  stoptime: 2019-09-19 19:56:08.437292
                  runtime: 0.0
                  description: |
                    A Simple Subsection

                            Use this docstring section to describe what is being done in this
                            subsection.

                            Note:
                                this subsection is empty and doing just about nothing. probably not
                                a good idea to submit to code reviews, sanity/regression.
                  xref:
                    file: /Users/user/examples/comprehensive/base_example.py
                    line: 249
                  logs:
                    begin: 106
                    file: TaskLog.Task-1
                    size: 419
                  result:
                    value: passed
                - ...
            - type: Testcase
              id: ExampleTestcase
              name: ExampleTestcase
              starttime: 2019-09-19 19:56:08.459497
              stoptime: 2019-09-19 19:56:08.488919
              runtime: 0.03
              description: An alternative description for this ExampleTestcase
              xref:
                file: /Users/user/examples/comprehensive/base_example.py
                line: 492
              logs:
                begin: 4317
                file: TaskLog.Task-1
                size: 7179
              parameters:
                labels: {}
                links: []
                local_A: default value A
                local_B: default value B
                parameter_A: jobfile value A
                parameter_B: value B
                routers: []
                testbed: <pyats.topology.testbed.Testbed object at 0x115da92b0>
                tgns: []
              processors:
                pre:
                  - type: Pre-processor
                    name: get_env_info
                    starttime: 2019-09-19 19:56:08.459518
                    stoptime: 2019-09-19 19:56:08.459733
                    runtime: 0.00
                    logs:
                      begin: 4325
                      file: TaskLog.Task-1
                      size: 264
                    returned:
                      env:
                        key1: val1
                        key2: val2
              result:
                value: errored
              sections:
                - type: SetupSection
                  id: setup
                  name: setup
                  starttime: 2019-09-19 19:56:08.460460
                  stoptime: 2019-09-19 19:56:08.462070
                  runtime: 0.0
                  description: |
                    Testcase Setup

                            This is where configuration specific to this testcase is carried out. In
                            addition, this section can also be used to verify that the test targets'
                            states are suitable for this testcase to be carried out
                  xref:
                    file: /Users/user/examples/comprehensive/base_example.py
                    line: 633
                  logs:
                    begin: 4436
                    file: TaskLog.Task-1
                    size: 345
                  result:
                    value: passed
                - type: TestSection
                  id: a_simple_test
                  name: a_simple_test
                  starttime: 2019-09-19 19:56:08.462577
                  stoptime: 2019-09-19 19:56:08.464354
                  runtime: 0.0
                  description: |
                    A simple Test

                            The simplest test section is simply a class method with @aetest.test
                            decorator.

                            No result APIs are called within this test section, and thus, as it
                            exits without error, it will be defaulted to Passed.
                  xref:
                    file: /Users/user/examples/comprehensive/base_example.py
                    line: 664
                  logs:
                    begin: 4781
                    file: TaskLog.Task-1
                    size: 460
                  result:
                    value: passed
                - ...
                - type: CleanupSection
                  id: cleanup
                  name: cleanup
                  starttime: 2019-09-19 19:56:08.487027
                  stoptime: 2019-09-19 19:56:08.488175
                  runtime: 0.0
                  description: |+
                    Testcase Cleanup

                            Testcase cleanup is always called as a last resort to cleanup the
                            test target of any changes made by this testcase. It should be written
                            in such a way that it always cleans up what's potentially left behind.

                            cleanup section is optional in each testcase.

                  xref:
                    file: /Users/user/examples/comprehensive/base_example.py
                    line: 773
                  logs:
                    begin: 11120
                    file: TaskLog.Task-1
                    size: 238
                  result:
                    value: passed
            - ...

Reporter uses a unix-socket client-server model to collect information about
each section of a job run. Clients make API calls to signal the start and end of
sections (testscripts, testcases, test methods, etc) with all of the relevant
information about that section of the job.

In addition to the *results.yaml* file, the reporter also produces
TRADe-compatible xml files *ResultsDetails.xml* and *ResultsSummary.xml* from
the aggregated results.

Reporter uses contexual reporters that share the hierarchy
of the testable sections instead of a single global reporter.

.. sectionauthor:: Ben Astell <bastell@cisco.com>
