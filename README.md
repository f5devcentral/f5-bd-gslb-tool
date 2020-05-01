Welcome to the **GSLB tool** project! 

**GSLB tool allows the automatic creation of GSLB DNS** entries in [F5 CloudServices](https://clouddocs.f5.com/cloud-services/latest/)' DNS LB service. This DNS LB service is a SaaS offering thus allowing to provision GSLB in minutes without provisioning of new infrastructure. 

**Verify that this GSLB solution suits your environment without any commitment**. You can try this tool and CloudService's in your existing production infrastructure without any change into your existing deployments.

In this page you will find an overview of the project. ![See the Wiki for setup and usage documentation](https://github.com/f5devcentral/f5-bd-cloudservices-gslb-tool/wiki). This is a WIP project. Stay tuned.

GSLB tool automatically retrieves **Layer 7 routes** (ie: `https://www.mycompany.com/shop`) from your infrastructure and automatically generates the GSLB configuration. The tool has several utilities which allow the move the workloads across the different deployments.

At present this tool focuses in Openshift clusters and [Route resources](https://docs.openshift.com/container-platform/3.11/architecture/networking/routes.html#route-types) but it could be easily extended to support any other Kubernetes with Ingress resources. GSLB tool has been tested with Openshift 3.x and 4.x.

The different members of a DevOps team can have the tool in their laptops and share the desired configuration in a git repository (source of truth). The overall architecture can be seen in the next diagram.

![Overall Architecture](https://raw.githubusercontent.com/f5devcentral/f5-bd-cloudservices-gslb-tool/master/diagrams/Diagram%20overall%20architecture.png)

GSLB tool doesn't require any special installation and doesn't modify your Openshift/K8s cluster (it only performs read-only operations). It is a set of Ansible playbooks and roles to allow  automation happen with a large degree of flexibility. GSLB tool is compromised of the following commands.

**project-** commands perform operations only on the source of truth (the Routes are not manipulated in the Openshift cluster):

* **project-retrieve**: retrieves all the routes of the given project/namespace and deployment.
* **project-populate**: populates (copies) the routes of a given project/namespace from one deployment to another.
* **project-evacuate**: evacuates (removes) from GSLB all the routes being hosted in the given project/namespace and deployment.
* **project-ratios**: sets the GSLB ratio for each deployment for a given project/namespace.

You can perform as many *project-* operations as desired and commit them at once into CloudServices. The commands that operate on CloudServices or related have the prefix *gslb-* on their name:

* **gslb-commit**: publishes into F5 Cloud Services the GSLB configuration stored in the source of truth.
* **gslb-rollback**: sets in the source of truth the configuration prior to the last commit. Needs that **gslb-commit** is run afterwards to make effective the rollback.

It's important to understand that whilst the **project-** commands operates on all the routes of a given project/namespace at a time, the **gslb-** commands operates on the whole source of truth which contains the desired state of the GSLB zone, with all the routes from all project-namespaces. When a **gslb-commit** command is executed it commits all the changes or doesn't commit any since the previous **gslb-commit** no matter how many **project-** operations have been performed previously.

![Operations animation](https://raw.githubusercontent.com/f5devcentral/f5-bd-gslb-tool/master/diagrams/Diagram%20Operations%20overview.png)

