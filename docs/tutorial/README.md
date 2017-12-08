# How to develop a REST api which integrate with a SOAP backend
May be it is yet another tutorial on IIB, but I found the product documentation a little bit unclear, and some existing videos / tutorial being too light. So how to really do a message flow that exposes RESTful apis and map to SOAP services. The source for the SOAP interface is defined in the data access layer project. This java application runs on liberty and is deployed on IBM Cloud Private exposed by Ingress rule to a host named: `dal.brown.case`. This application exposes CRUD operations for three entities: item, inventory and supplier. Imagine it was done ten years ago. Today it will be split into two or even three micro services, one per entity. In fact it will depend of the ownership of the business logic. In this case we can imagine one group own the inventory management.

## Pre-requisite
for development purpose we will use a docker image with the toolkit installed. For instruction on how we did build the mq, iib runtime and iib-toolkit image read [this note](../../docker/README.md)

## Step by step tutorial
The solution expose a RESTapi to be consumed by API Connect or directly from web application. As introduce in [this knowledge center note](ttps://developer.ibm.com/integration/docs/ibm-integration-bus/get-started-developing-an-integration-solution-overview/) the steps to consider for developing an integration application are:  

## 1- Select the implementation style
We start by using a REST api, so focusing on the consumer contract. Start the Integration toolkit with the command `iib toolkit`

## 2 - Decide on reusable content that will be part of Library.
We like reuse. Payload definition like item, inventory, supplier object may be reusable.
1. Create a REST API application using the Integration toolkit product.
1. Define the resources and operations that will be exposed by the API using a swagger file. (See file in integration/InventoryRESapi/inventory-api_1.0.0.yaml)
1. Define the data model (e.g. item and items objects were defined in the swagger file too).
1. Import the WSDL to consumer using the Web Services Explorer and save the wsdl into your project / workbench.
1. For each operation create a subflow: the flow has input and output node,
 * drag and drop SOAP request node and map the node property to the SOAP operation of the WSDL. For example getId is mapped to `itemById` binding operation.
 * Define maps to map REST input to XML input and then XML ouput response to JSON object. (see [this not in knowledge center ](https://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/sm12030_.htm))

If you want to access the project, open the IIB toolkit and use import > General > Import existing project, then select the `refarch-integration-esb/integration/InventoryRESTapi` project.

In the API Description the base URL is set to **iib-inventory-api** (this is defined in the header section), then for the resources the following operations are defined:
```
* /item/{id} GET, PUT, DELETE
* /items GET, POST
```