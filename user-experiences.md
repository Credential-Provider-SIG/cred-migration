# Desired User Experiences for CRIMP
Credential transfer must occur with user consent and intent in mind. This section outlines several different flows for how credential import and export can be used and what the user's involvement in the process would look like.

## Same Device Flows
In many cases, a credential owner will be attempting to import credentials from one provider to another on the same device. Some of these flows may 

### Application to Application
A user has installed or been instructed to install a new credential provider and wants to migrate the contents of their existing credential provider to the new service. The user would go to the new provider and request export of some types or all credentials from another provider. The platform will then prompt the user to choose the intended export provider, which the user can then select. After making their selection, the exporting credential provider will either confirm with the user that they're going to export their credentials, and the user will authorize the release. Once the credentials are imported, the importer will inform the user that the operation was completed and the success of the transfer.

## Browser Extension to Application
A user has installed a browser-based credential provider that is accessable view 

## Device to Device Flows
There are several different modalities through which a credential owner can migrate across devices, and depending on network or policy requirements, the means with which they transfer credentials may differ. Below outlines several different device to device flows depending on networking and policy constraints. 

### Online Optional Flow
A credential owner agent has been given a new phone or laptop and wishes to move credentials from an application on one device to another without yet having network access between both devices. They begin to set up the new device and import the data from the existing device. On the new device, they request that the application import their existing credentials from the previous device. The two devices are connected via cable or local network, and the credentials are moved to the new device for the user.

### Online Dependent Flow
A user receives a device from their company with a new credential provider and wants to transfer credentials from an existing provider on their previous company-owned device. The user goes into the new credential manager and requests that an "export link" be generated 

### Online Optional Hybrid Flow 
#### Between Desktop/Mobile to Mobile Devices
This flow can be used to support transfer between Desktop/Mobile to Mobile Devices. A user 