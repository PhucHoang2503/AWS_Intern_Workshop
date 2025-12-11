---
title: "Blog 1"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Enhance governance with metadata enforcement rules in Amazon SageMaker

by Pradeep Misra, Ramesh H Singh, and Sandhya Edupuganti | on 28 MAR 2025 in | **Amazon SageMaker Lakehouse**, **Analytics**, **Announcements**, **Technical How-to**

**The next generation of SageMaker** brings together widely adopted AWS machine learning and analytics capabilities, delivering an integrated experience with unified access to all data. **Amazon SageMaker Lakehouse** supports unified data access, and **Amazon SageMaker Catalog**, built on Amazon DataZone, offers catalog and governance features to meet enterprise security needs. Amazon SageMaker Catalog now supports metadata rules allowing organizations to enforce metadata standards across data publishing and subscription workflows.

A rule is a formal agreement that enforces specific metadata requirements across user workflows (e.g., publishing assets to the catalog, requesting data access) within the **Amazon SageMaker Unified Studio** portal. For instance, a metadata enforcement rule can specify the required information for creating a **subscription request** or **publishing a data asset** or a data product to the catalog, ensuring alignment with organizational standards. Metadata rules also enable the creation of custom approval workflows for subscriptions to assets, using collected metadata to facilitate access decisions or auto-fulfillment—outside of SageMaker.

By standardizing metadata practices, Amazon SageMaker Catalog enables customers to meet compliance requirements, enhance audit readiness, and streamline access workflows for greater efficiency and control. One such customer is Amazon Shipping Tech, which uses SageMaker Catalog for cataloging, discovery, sharing, and governance across their data ecosystem:

> “We’re building an Analytics Ecosystem to drive discovery across the organization—but without consistent metadata, even our most valuable data can go unused. This feature empowers more teams to actively contribute to metadata curation with the right governance in place. It allows us to set clear standards for data producers while streamlining the collection of required subscription details—no extra templates needed.  
> By enforcing standard metadata attributes, we improve discoverability, add context to each request, and strengthen support for analytics and GenAI solutions.”
>
> — Saurabh Pandey, Principal Data Engineer at Amazon Shipping Tech

### Sample use-cases

Metadata rules could help in the following use cases:

- A producer at an automobile company is preparing to publish a new dataset into the organization’s **data catalog**. The domain owner for the automotive domain requires that the producer include metadata fields such as Model Year, Region, and Compliance Status. Before the dataset can be published, automated checks make sure that these fields are correctly filled out according to the predefined standards.
- A consumer is requesting access to data assets in SageMaker. To meet organization standards and support audit and reporting needs, they must complete the subscription request, fill out a detailed form that includes the project purpose, and attach an email link with pre-approval and compliance training evidence to request subscription for the financial data product. The data owner reviews the request, checking that all required metadata are provided before granting access.

### Key benefits

Key benefits of new metadata enforcement rules include:

- **Enhanced control for domain (unit) owners** – Admins can enforce additional metadata fields on subscription and publishing workflows, which must be adhered to by data users. This process supports thorough reviews and enforces organizational compliance.
- **Custom workflow support** – You can create custom workflows for fulfilling subscriptions on non-managed assets by capturing essential metadata from data consumers. This metadata is used to configure access or support specific business requirements.

In this post, we guide you through two workflows: setting up metadata enforcement rules for a specific domain and publishing an asset or data product in a catalog, and setting up metadata enforcement rules for a specific domain and subscribing to an asset or data product that is owned by a project within that domain.

## Solution Overview: Metadata Enforcement for Publishing

In this solution, we’ll walk through two workflows: setting up metadata enforcement for publishing, and setting up metadata enforcement for subscription.

### Prerequisites

To follow this post, you should have a SageMaker Unified Studio domain set up with a domain owner or domain unit owner privileges. For instructions, refer to the **Getting started** guide.

## Set up metadata enforcement for publishing

In this section, we show you how to set up metadata rules for a specific domain as a domain admin. We also explain what happens when you publish an asset or data product in a catalog with these rules applied.

### Create a domain unit for the marketing team

As a domain admin, complete the following steps:

1. On the SageMaker Unified Studio console, choose the **Govern** dropdown menu and choose **Domain units**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image001.png)
2. Choose **CREATE DOMAIN UNIT**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image003.png)
3. Provide details shown in the following screenshot and choose **CREATE DOMAIN UNIT**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image005.png)
   You can see the domain unit as shown in the following screenshot.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image007.png)

### Enable a metadata form creation policy in the Marketing domain unit

1. Navigate to the **AUTHORIZATION POLICIES** tab in the **Marketing** domain unit and choose **Metadata form creation policy**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image009.png)
2. Choose **ADD POLICY GRANT**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image011.png)
3. Select **All projects in a domain unit** and add a policy grant.
4. You can also select specific projects that can create metadata forms.
5. Choose **ADD POLICY GRANT**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image013.png)
   You can see the policy now created for the **Marketing** domain unit.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image015.png)

### Create a metadata form to be enforced for assets before publishing

1. In the `publish-1` project, choose **Metadata entities** under **Project catalog** in the navigation pane.
2. On the **Metadata forms** tab, choose **CREATE METADATA FORM**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image017.png)
3. Provide a display name, technical name, and description.
4. Choose **CREATE METADATA FORM**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image019.png)
5. After you create the form, you can choose **CREATE FIELD** to enforce fields that should be there in all published assets.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image021.png)
6. Provide details as shown in the following screenshot.
7. Select **Searchable**, **Required**, and **Publishing** because these fields are required before publishing.
8. Choose **CREATE FIELD**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image023.png)
9. Add another field as shown in the following screenshot.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image025.png)

Both fields created with the **Publishing** action will require values before publishing to the catalog.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image027.png)

### Create rules for asset publishing

1. In the `publish-1` project, under Domain Management in the navigation pane, choose Domain units.
2. Choose the **Marketing domain unit**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image029.png)
3. On the **Rules** tab, choose **ADD**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image031.png)
4. Create the rule configuration with details in the following screenshot and add the metadata form created in the previous step.
5. You can select the scope of enforcement by asset type and projects.
6. Choose **ADD RULE** to create the rule.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image033.png)

The publishing enforcement rule `publish_rules` is now created.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image035.png)

### Create a project in the Marketing domain unit

Create a project named `publish-1` in the Marketing domain unit. To learn how to create a project, refer to **Create a project**.

### Create an asset in the project

Rules work on assets managed by the SageMaker Catalog or on custom assets. To create an asset, complete the following steps:

1. In the `publish-1` project, choose **Assets** under **Project catalog** in the navigation pane.
2. On the **Create** dropdown menu, choose **Create asset**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image037.png)
3. Provide an asset name and description, then choose **Next**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image039.png)
   For this solution, you will create an **Amazon Simple Storage Service** (Amazon S3) object collection.
4. For **Asset type**, choose **S3 object collection**.
5. For **S3 location ARN**, enter the Amazon Resource Name (ARN) of the S3 object.
6. Choose **Next**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image041.png)
7. Choose **CREATE**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image043.png)
   The asset `marketing_campaign_asset` is now created. This is still an inventory asset and not published to the catalog.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image045.png)

### Publish rules enforcement

Asset details now show that the required values are missing for the mandatory form `Publish_form`.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image047.png)
You can try to publish without the required fields and the system will throw an error to enforce publishing metadata rules.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image049.png)
To fix the issue, edit the value for the metadata form to provide the required info.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image051.png)
Provide details for the fields and choose **SAVE**.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image053.png)
Choose **PUBLISH ASSET** now and the asset will be published to the catalog.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image055.png)
You can see the asset is published with the required fields enforced with rules.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image057.png)

## Set up metadata enforcement for subscription requests

In this section, we show you how to set up metadata rules for a specific domain as a domain admin. We also explain what happens when you subscribe to an asset or data product with these rules applied.

### Create rules for asset subscription

1. Navigate to the project used in the previous section and choose **Metadata entities** under **Project catalog** in the navigation pane.
2. On the **Metadata forms** tab, choose **CREATE METADATA FORM** to create a new form.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image059.png)
3. Provide a form name and description, then choose **CREATE METADATA FORM**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image061.png)
4. Add fields to the form by choosing **CREATE FIELD** and turning on **Enabled**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image063.png)
5. Add a field for subscribers to explain the use case when requesting access.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image065.png)

### Create rules for asset subscription

Complete the following steps:

1.  On the project page, choose Domain units under Domain Management in the navigation pane.
2.  Choose the **Marketing** domain unit.
    ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image067.png)
    We already have a publishing rule.
3.  On the **Rules** tab, choose **ADD** to add a new rule.
    ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image069.png)
4.  Provide details for the new rule.
5.  Specify the action as **Subscription request**.
6.  Add the metadata form created in the previous steps (`Subscribe_form`).
7.  Choose the scope and projects for enforcement as shown in the following screenshot.
8.  Choose **ADD RULE**.
    ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image071.png)
    You will see the subscription enforcement rule is now created.

### Subscribe the asset

Complete the following steps to subscribe the asset:

1. On the project page, navigate to the marketing asset.
2. Choose **SUBSCRIBE**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image073.png)
   The subscribe form is now attached in the request for the user to provide information.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image075.png)

After a data consumer submits a subscription request, the data producer receives it along with the provided metadata—such as **Use Case**. This allows producers to review the request before granting access.

## Clean up

To avoid incurring additional charges, delete the Amazon SageMaker domain. Refer to **Delete domains** for the process.

## Conclusion

In this post, we discussed metadata rules and how to implement them for both publishing and subscribing to assets across different domains, demonstrating effective metadata governance practices.

The new metadata enforcement rule in Amazon SageMaker strengthens data governance by enabling domain unit owners to establish clear metadata requirements for data users, streamlining catalog health and enhancing data governance process for access request. This feature enables organizations to align with organization’s metadata standards, implement custom workflows, and provide a consistent, governed data workflow experience.

The feature is supported in AWS Commercial Regions where Amazon SageMaker is currently available. To get started with metadata rules—

Read the [user guide](https://docs.aws.amazon.com/sagemaker-unified-studio/latest/userguide/metadata-rules-publishing.html) for creating rules in the publishing workflow

Read the [user guide](https://docs.aws.amazon.com/sagemaker-unified-studio/latest/userguide/metadata-rules-subscription.html) for creating rules in subscription requests

---

### About the Authors

![Blog-1](/images/3-Blog/Blog-1/pkmisra-1.png)
**Pradeep Misra** is a Principal Analytics Solutions Architect at AWS. He works across Amazon to architect and design modern distributed analytics and AI/ML platform solutions. He is passionate about solving customer challenges using data, analytics, and AI/ML. Outside of work, Pradeep likes exploring new places, trying new cuisines, and playing board games with his family. He also likes doing science experiments, building LEGOs and watching anime with his daughters.

![Blog-1](/images/3-Blog/Blog-1/RAMESH-H-SINGH-PHONE-TOOL.png)
**Ramesh H Singh** is a Senior Product Manager Technical (External Services) at AWS in Seattle, Washington, currently with the Amazon SageMaker team. He is passionate about building high-performance ML/AI and analytics products that enable enterprise customers to achieve their critical goals using cutting-edge technology. Connect with him on [LinkedIn](http://www.linkedin.com/in/ramesh-harisaran-singh).

![Blog-1](/images/3-Blog/Blog-1/sandhyae.png)
**Sandhya Edupuganti** is a Senior Engineering Leader spearheading Amazon DataZone (aka) SageMaker Catalog. She is based in Seattle Metro area and has been with Amazon for over 17 years leading strategic initiatives in Amazon Advertising, Amazon-Retail, Latam-Expansion and AWS Analytics.
