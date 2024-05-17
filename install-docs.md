
Installing the Compliance Operator

Before you can use the Compliance Operator, you must ensure it is deployed in the cluster.
	

The Compliance Operator might report incorrect results on managed platforms, such as OpenShift Dedicated, Red Hat OpenShift Service on AWS, and Microsoft Azure Red Hat OpenShift. For more information, see the Red Hat Knowledgebase Solution #6983418.
Installing the Compliance Operator through the web console
Prerequisites

    You must have admin privileges.

Procedure

    In the OpenShift Container Platform web console, navigate to Operators → OperatorHub.

    Search for the Compliance Operator, then click Install.

    Keep the default selection of Installation mode and namespace to ensure that the Operator will be installed to the openshift-compliance namespace.

    Click Install.

Verification

To confirm that the installation is successful:

    Navigate to the Operators → Installed Operators page.

    Check that the Compliance Operator is installed in the openshift-compliance namespace and its status is Succeeded.

If the Operator is not installed successfully:

    Navigate to the Operators → Installed Operators page and inspect the Status column for any errors or failures.

    Navigate to the Workloads → Pods page and check the logs in any pods in the openshift-compliance project that are reporting issues.

	

If the restricted Security Context Constraints (SCC) have been modified to contain the system:authenticated group or has added requiredDropCapabilities, the Compliance Operator may not function properly due to permissions issues.

You can create a custom SCC for the Compliance Operator scanner pod service account. For more information, see Creating a custom SCC for the Compliance Operator.
Installing the Compliance Operator using the CLI
Prerequisites

    You must have admin privileges.

Procedure

    Define a Namespace object:
    Example namespace-object.yaml

    apiVersion: v1
    kind: Namespace
    metadata:
      labels:
        openshift.io/cluster-monitoring: "true"
        pod-security.kubernetes.io/enforce: privileged 
      name: openshift-compliance

    	In OpenShift Container Platform 4.15, the pod security label must be set to privileged at the namespace level.

    Create the Namespace object:

    $ oc create -f namespace-object.yaml

    Define an OperatorGroup object:
    Example operator-group-object.yaml

    apiVersion: operators.coreos.com/v1
    kind: OperatorGroup
    metadata:
      name: compliance-operator
      namespace: openshift-compliance
    spec:
      targetNamespaces:
      - openshift-compliance

    Create the OperatorGroup object:

    $ oc create -f operator-group-object.yaml

    Define a Subscription object:
    Example subscription-object.yaml

    apiVersion: operators.coreos.com/v1alpha1
    kind: Subscription
    metadata:
      name: compliance-operator-sub
      namespace: openshift-compliance
    spec:
      channel: "stable"
      installPlanApproval: Automatic
      name: compliance-operator
      source: redhat-operators
      sourceNamespace: openshift-marketplace

    Create the Subscription object:

    $ oc create -f subscription-object.yaml

	

If you are setting the global scheduler feature and enable defaultNodeSelector, you must create the namespace manually and update the annotations of the openshift-compliance namespace, or the namespace where the Compliance Operator was installed, with openshift.io/node-selector: “”. This removes the default node selector and prevents deployment failures.
Verification

    Verify the installation succeeded by inspecting the CSV file:

    $ oc get csv -n openshift-compliance

    Verify that the Compliance Operator is up and running:

    $ oc get deploy -n openshift-compliance

	

If the restricted Security Context Constraints (SCC) have been modified to contain the system:authenticated group or has added requiredDropCapabilities, the Compliance Operator may not function properly due to permissions issues.

You can create a custom SCC for the Compliance Operator scanner pod service account. For more information, see Creating a custom SCC for the Compliance Operator.
Installing the Compliance Operator on hosted control planes

The Compliance Operator can be installed in hosted control planes using the OperatorHub by creating a Subscription file.
	

Hosted control planes is a Technology Preview feature only. Technology Preview features are not supported with Red Hat production service level agreements (SLAs) and might not be functionally complete. Red Hat does not recommend using them in production. These features provide early access to upcoming product features, enabling customers to test functionality and provide feedback during the development process.

For more information about the support scope of Red Hat Technology Preview features, see Technology Preview Features Support Scope.
Prerequisites

    You must have admin privileges.

Procedure

    Define a Namespace object similar to the following:
    Example namespace-object.yaml

    apiVersion: v1
    kind: Namespace
    metadata:
      labels:
        openshift.io/cluster-monitoring: "true"
        pod-security.kubernetes.io/enforce: privileged 
      name: openshift-compliance

    	In OpenShift Container Platform 4.15, the pod security label must be set to privileged at the namespace level.

    Create the Namespace object by running the following command:

    $ oc create -f namespace-object.yaml

    Define an OperatorGroup object:
    Example operator-group-object.yaml

    apiVersion: operators.coreos.com/v1
    kind: OperatorGroup
    metadata:
      name: compliance-operator
      namespace: openshift-compliance
    spec:
      targetNamespaces:
      - openshift-compliance

    Create the OperatorGroup object by running the following command:

    $ oc create -f operator-group-object.yaml

    Define a Subscription object:
    Example subscription-object.yaml

    apiVersion: operators.coreos.com/v1alpha1
    kind: Subscription
    metadata:
      name: compliance-operator-sub
      namespace: openshift-compliance
    spec:
      channel: "stable"
      installPlanApproval: Automatic
      name: compliance-operator
      source: redhat-operators
      sourceNamespace: openshift-marketplace
      config:
        nodeSelector:
          node-role.kubernetes.io/worker: ""
        env:
        - name: PLATFORM
          value: "HyperShift"

    Create the Subscription object by running the following command:

    $ oc create -f subscription-object.yaml

Verification

    Verify the installation succeeded by inspecting the CSV file by running the following command:

    $ oc get csv -n openshift-compliance

    Verify that the Compliance Operator is up and running by running the following command:

    $ oc get deploy -n openshift-compliance

Additional resources

    Hosted control planes overview

Additional resources

    The Compliance Operator is supported in a restricted network environment. For more information, see Using Operator Lifecycle Manager on restricted networks.


