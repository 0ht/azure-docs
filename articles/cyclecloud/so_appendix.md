# Submit Once Appendices

## Appendix A: Two-Cluster Job Workflow

The diagram below shows the SubmitOnce workflow when there are two Grid Engine clusters (one local, one remote):

![Two-Cluster Job Workflow Diagram](~/images/two_gecluster_workflow.png)

## Appendix B: Configuring Grid Engine Clusters with No Shared SubmitOnce Home Directory

Though not recommended, it is possible to configure SubmitOnce to use an $SO_HOME directory that is not shared within a given Grid Engine cluster. Using this approach, file transfer jobs are run on the head node instead of on execute nodes. To do this, follow the steps below:

  #. Create $SO_HOME on each head node, owned by the cycle_server user.

  #. Define a string complex on each submitter called "slot_type" and set the value to "master". Follow the steps below to define this complex.

      a. Edit the list of complex attributes by running the command::

          qconf -mc

      #. Add a new line::

          slot_type           slot_type    RESTRING    ==    FORCED      NO         NONE     0

      #. Run the following command to open the submitter's configuration::

          qconf -me submitter_host.example.com

      #. Add or edit the existing "complex_values" entry to include the following::

          complex_values        slot_type=master

  #. Make sure that each head node has a number of slots equal to the desired concurrent file transfers you wish to allow.

  #. In the CycleServer web interface, go to "Admin > System Settings" in the top right-hand menu, double-click "SubmitOnce" and uncheck the "Shared and mounted cluster-wide" checkbox.
