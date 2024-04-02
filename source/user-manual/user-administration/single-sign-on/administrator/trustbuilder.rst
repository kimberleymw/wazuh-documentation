.. Copyright (C) 2015, Wazuh, Inc.

.. meta::
   :description: TrustBuilder is a CIAM platform.

TrustBuilder
============

`TrustBuilder <https://www.trustbuilder.com/>`_ is a cloud-based CIAM (Customer Identity and Access Management) provider. In this guide, we integrate the TrustBuilder IdP to authenticate users into the Wazuh platform.

There are three stages in the single sign-on integration.

#. `TrustBuilder Configuration`_
#. `Wazuh indexer configuration`_
#. `Wazuh dashboard configuration`_

TrustBuilder Configuration
--------------------------

#. Login to TrustBuilder Admin Portal.

#. Create and configure Wazuh as TrustBuilder Service Provider: 

   #. Navigate to **Access Management** > **Service Providers** > **+ Add a new SP**.

   #. Complete the following fields:

      - Dipslay name: Enter a name  ``wazuh``
      - Authentication scheme: Select the Authentication scheme. It defines which IDP(s) can authenticate a user for this Service Provider, and how the user can authenticate.
      - Subject: Select the primary user attribute used to identify the user.``Email``
      - Certificate: Add a certificate.``Context: SIGNING (KEY)``, ``Certificate: idhub-signing``.
      - Entity ID: Enter your Wazuh tenant URL.``https://xxxxxxxx.cloud.wazuh.com/``
      - Assertion Signed: Define whether the assertion from the TrustBuilder to the Service Provider will be signed or not.``True``
      - Encrypted type: Define which part of the assertion is encrypted.``NONE``
      - ACS Endpoint: Click on **+ Add ACS endpoint**. It is the endpoint to which TrustBuilder will send SAML assertions after the user has been successfully authenticated.
        - Binding:  ``HTTP-Post``
        - Location: ``https://<WAZUH_DASHBOARD_URL>/_opendistro/_security/saml/acs``
      - SLO Endpoint: Click on + Add SLO endpoint. It is the endpoint provided by Wazuh where TrustBuilder can send logout requests.
        - Binding: ``HTTP-Redirect``
        - Location: ``https://<WAZUH_DASHBOARD_URL>/_opendistro/_security/saml/acs ``
      - Include 509 certificate: Specify if it should include the complete certificate in the signature. or not.``True``
      - Signature method: Define which algorithm is used to sign the assertion.``RSA SHA256``
      - Post Profile Template: ``Default SAML POST page``
      - Subject Recipient Strategy: Specify the location where to present the assertion.``ACS URI``

   #. Click on **Save & Close**.

#. Create two attributes:

   a. Navigate to **Identity Management** > **Attributes** > **Attributes Definition**.

   b. Click on **+ Add new user Attributes**. Complete the first attribute parameters: 

      - Category: ``wazuh``
      - Name: ``group``
      - Dipslay name: ``wazuh group``
      - Data format: ``TEXT``
      - Properties: ``Single Value``,``Read Only``, ``Hidden``

      Click on **Save & Close**.

   c. Click on **+ Add new user Attributes**. Complete the second attribute parameters:

      - Category: ``wazuh``
      - Name: ``role``
      - Dipslay name: ``wazuh role``
      - Data format: ``TEXT``
      - Properties: ``Single Value``,``Read Only``, ``Hidden``
 
      Click on **Save & Close**.

#. Add Wazuh attributes to user profile definition: 

   #. Navigate to **Identity Management** > **User Profile Definition**.
 
   #. Select ``wazuh role`` and ``wazuh group``.

   #. Click on **Save**.

#. Add Wazuh attibrutes to the SP:

   #. Navigate to **Access management** > **Service Providers**. 
   
   #. Search for the Wazuh service provider created in the previous step.

   #. Click on **Identity**.

   #. Click twice on **+ Add New SP User Attribute**. 

   #. Complete the fields: 

      - Service Provider user attribute: ``Role``
      - User attribute: ``wazuh group``
      - Required: ``Yes``

      - Service Provider user attribute: ``roles_key``
      - User attribute: ``wazuh role``
      - Required: ``Yes``

   #. Click on **Save & Close**.
