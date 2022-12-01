# Customers and Service Partners

Latch is focused on building great solutions to serve both our customers and our partners. 

For customers, we provide industry-leading, Latch-enabled hardware and intuitive software to provide the best end-user experience. For example, with Latch Manager, property managers can easily administer and remotely control all the Latch devices within their properties.

For partners, we are building world-class SDKs and APIs to support mutual customers by enabling Latch features for different management platforms, resident experiences, and building services. Through our SDKs and APIs, we can deliver new products to end-users by leveraging a combination of partner and Latch systems to manage Latch hardware.

| Type | Function |
| ---- | -------- |
| End User | Utilizes the Partner App with Unlock Kit to Unlock Latch devices. |
| Customer | Purchases and manages Latch devices. |
| Partner | Utilizes the Unlock Kit and User Kit. |
| Customer Partner | Purchases Latch devices and utilizes the Unlock Kit and User Kit. |

## Responsibilities

When implementing the Latch Unlock Kit and User Kit, Partners work together with Latch to provide a great user experience to their customers and end-users. Below is a listing of some of the customer and partner responsibilities. Customer Partner responsibilities are the sum of customer and partner responsibilities.

| Customer | Partner | Latch |
| -------- | ------- | ----- |
| Manage Physical Devices | Build client and server integration with Latch | Provide access to APIs, SDKs and documentation |
| Setup Doors and Keys | User Authentication | Partner Authentication (Static IP Restricted) |
| Assign Doors/Keys to Partners | User Management | Partner / User Authorization |
| | JWT Token retrieval from Latch BE | JWT Token Provider |
| | Host App for Latch SDK | Native IOS/Android SDKs |
| | Renew JWT Token | Secure SDK storage and communication |
| | Provide a great UI/UX | Provide and Cache Device Credentials |
| | Gather Bluetooth Permissions | Encapsulate all BLE Interactivity |
| | Static IPs | Provide a list of Doors and Devices to App |
| | | Gather User Consent |
| | | Traffic Monitoring |




