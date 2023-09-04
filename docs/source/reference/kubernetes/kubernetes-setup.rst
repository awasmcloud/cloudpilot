.. _kubernetes-setup:

Kubernetes Cluster Setup
========================

SkyPilot on Kubernetes is designed to work with most Kubernetes distributions and deployment environments.

To connect to a Kubernetes cluster, SkyPilot needs:

* An existing Kubernetes cluster running Kubernetes v1.20 or later.
* A `Kubeconfig <kubeconfig>`_ file containing access credentials and namespace to be used.

Below we show minimal examples to setup a new Kubernetes cluster in different environments, including hosted services on the cloud, and generating kubeconfig files which can be :ref:`used by SkyPilot <kubernetes-instructions>`.

TODO (Add image grid 4x4 - Kind,  GKE, EKS, On-Prem).

Deploying locally on your Laptop
--------------------------------

If you want to try out SkyPilot on Kubernetes on your laptop or run SkyPilot
tasks locally without requiring any cloud access, we provide the
:code:`sky local up` CLI to create a 1-node Kubernetes cluster locally.

Under the hood, :code:`sky local up` uses `kind <https://kind.sigs.k8s.io/>`_,
a tool for creating a Kubernetes cluster on your local machine.
It runs a Kubernetes cluster inside a container, so no setup is required.

1. Install `Docker <https://docs.docker.com/engine/install/>`_ and `kind <https://kind.sigs.k8s.io/docs/user/quick-start/#installation>`_.
2. Run :code:`sky local up` to launch a Kubernetes cluster and automatically configure your kubeconfig file:

    .. code-block:: console

        $ sky local up

3. Run :code:`sky check` and verify that Kubernetes is enabled in SkyPilot. You can now run SkyPilot tasks on this locally hosted Kubernetes cluster using :code:`sky launch`.
4. After you are done using the cluster, you can remove it with :code:`sky local down`. This will terminate the KinD container and switch your kubeconfig back to it's original context:

    .. code-block:: console

        $ sky local down

.. note::
    We recommend allocating at least 4 or more CPUs to your docker runtime to
    ensure kind has enough resources. See instructions
    `here <https://docs.docker.com/desktop/settings/linux/>`_.

.. note::
    KinD does not support multiple nodes and GPUs.
    It is not recommended for use in a production environment.
    If you want to run a private On-Prem cluster, see the section on `On-Prem deployment <Deploying on On-Prem Clusters>`_ for more.

Deploying on GKE
----------------

1. Create a GKE standard cluster with at least 1 node. We recommend creating nodes with at least 4 vCPUs.
2. Get the kubeconfig for your cluster. This will automatically update ``~/.kube/config`` with new kubecontext for the GKE cluster. :

    .. code-block:: console

        $ gcloud container clusters get-credentials <cluster-name> --region <region>

        # Example:
        # gcloud container clusters get-credentials testcluster --region us-central1-c

3. [If using GPUs] If your GKE nodes have GPUs, you may need to to
   `manually install <https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/>`_
   nvidia drivers. You can do so by deploying the daemonset
   depending on the OS of your nodes:

    .. code-block:: console

        # For Container Optimized OS (COS) based nodes:
        $ kubectl apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/container-engine-accelerators/master/nvidia-driver-installer/cos/daemonset-preloaded.yaml

        # For Ubuntu (COS) based nodes:
        kubectl apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/container-engine-accelerators/master/nvidia-driver-installer/ubuntu/daemonset-preloaded.yaml

   To verify if GPU drivers are setup, run TODO - add oneliner to run k describe nodes and list resources.

4. Verify your kubeconfig is correctly setup by running :code:`sky check`:

    .. code-block:: console

        $ sky check

.. note::
    GKE autopilot clusters are currently not supported. Only GKE standard clusters are supported.


Deploying on AWS EKS
--------------------

TODO(romilb): Test and add this.


Deploying on On-Prem Clusters
-----------------------------

You can also deploy Kubernetes on your On-Prem clusters using off-the-shelf tools, such as `kubeadm <https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/>`_, `k3s <https://docs.k3s.io/quick-start>`_ or `Rancher <https://ranchermanager.docs.rancher.com/v2.5/pages-for-subheaders/kubernetes-clusters-in-rancher-setup>`_. Please follow their respective guides to deploy your Kubernetes cluster.

Once the cluster is deployed, make sure:

1. You have a kubeconfig file for accessing the cluster.

If all looks good, follow instructions :ref:`here <kubernetes-instructions>` to setup Kubernetes access for each SkyPilot client.