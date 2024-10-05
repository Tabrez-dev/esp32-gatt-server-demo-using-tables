# esp32-gatt-server-demo-using-tables

![WhatsApp Image 2024-10-05 at 4 25 57 PM](https://github.com/user-attachments/assets/6132fec7-006f-499b-a3ae-017e99e447ae)

You can observe the changes by running the code on esp32 and connecting to it using BLE Connect application
In Bluetooth Low Energy (BLE) development using ESP32, there are two primary methods to define and manage GATT attributes when creating services: the manual attribute-by-attribute approach (Bluedroid method) and the more streamlined attribute table approach. Both methods have their merits, but they differ significantly in terms of complexity, ease of use, and flexibility.
1. Manual Attribute-by-Attribute Approach (Bluedroid Method):

This traditional method is deeply integrated into the Bluedroid Bluetooth stack, which is used in the ESP32 framework. In this approach, each GATT service and its associated characteristics and descriptors are added individually, one after the other. For each service or characteristic, the developer needs to manually specify properties like UUIDs, permissions, and handles. Each of these GATT elements must be registered and initialized step-by-step within the code.

The process typically involves:

    Defining each service (like primary or secondary services).
    Adding characteristics to the service one by one.
    Assigning descriptors (such as Client Characteristic Configuration Descriptor, or CCCD) if the characteristic supports notifications or indications.

While this method gives developers fine-grained control over the definition of each element, it becomes cumbersome for large or complex BLE setups. For every service and characteristic, multiple lines of code need to be written, making the process repetitive and error-prone, especially when handling many characteristics and services. This can also lead to difficulty in maintaining the codebase, as any addition or modification requires updating multiple code segments.
2. Attribute Table Approach:

In contrast, the attribute table approach simplifies the management of GATT services and their associated attributes by allowing developers to define all attributes in a single, structured array. This table contains the entire set of services, characteristics, and descriptors at once, rather than manually adding them one by one. In this method, the attribute table encapsulates the full layout of the GATT profile (UUIDs, permissions, properties, etc.) in a cleaner, more organized manner.

Instead of calling separate functions to add each element manually, the developer defines the attribute structure in a central location, which is then loaded and processed by the BLE stack as a whole. This results in a more readable and maintainable codebase, particularly when dealing with more than one service or many characteristics. The attribute table approach essentially abstracts away the low-level details of adding individual attributes, enabling a higher level of code organization.

For example, all attributes related to a service—such as its UUID, its associated characteristics, and their descriptors—are defined in a structured array. The BLE stack then processes this table at once, registering all attributes simultaneously. This eliminates much of the repetitive code seen in the manual approach and reduces the potential for errors that come with manual handling.
Key Differences:

    Complexity: The manual approach requires the developer to manage every service, characteristic, and descriptor individually, leading to more complex and repetitive code. In contrast, the attribute table approach simplifies this by encapsulating all GATT attributes into a single, easy-to-manage table.

    Ease of Use: The attribute table approach is generally more user-friendly, especially for developers new to BLE or those working with large, complex profiles. The manual approach offers more granular control but at the cost of increased complexity.

    Flexibility: While the manual approach may be more flexible for custom setups, it is also more error-prone and difficult to maintain. The attribute table approach offers sufficient flexibility for most use cases while keeping the codebase organized and scalable.

    Maintenance: With the manual approach, maintaining the code becomes harder as the GATT profile grows in complexity, requiring updates in multiple places for each new attribute. The attribute table approach, by contrast, offers a single point of definition, making maintenance more straightforward.
