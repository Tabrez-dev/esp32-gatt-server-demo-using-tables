# esp32-gatt-server-demo-using-tables

![WhatsApp Image 2024-10-05 at 4 25 57 PM](https://github.com/user-attachments/assets/6132fec7-006f-499b-a3ae-017e99e447ae)

You can observe the changes by running the code on esp32 and connecting to it using BLE Connect application
<br>
<br>
In Bluetooth Low Energy (BLE) development using ESP32, there are two primary methods to define and manage GATT attributes when creating services: the manual attribute-by-attribute approach (Bluedroid method) and the more streamlined attribute table approach. Both methods have their merits, but they differ significantly in terms of complexity, ease of use, and flexibility.

# 1. Manual Attribute-by-Attribute Approach (Bluedroid Method):

This traditional method is deeply integrated into the Bluedroid Bluetooth stack, which is used in the ESP32 framework. In this approach, each GATT service and its associated characteristics and descriptors are added individually, one after the other. For each service or characteristic, the developer needs to manually specify properties like UUIDs, permissions, and handles. Each of these GATT elements must be registered and initialized step-by-step within the code.

The process typically involves:

    Defining each service (like primary or secondary services).
    Adding characteristics to the service one by one.
    Assigning descriptors (such as Client Characteristic Configuration Descriptor, or CCCD) if the characteristic supports notifications or indications.

While this method gives developers fine-grained control over the definition of each element, it becomes cumbersome for large or complex BLE setups. For every service and characteristic, multiple lines of code need to be written, making the process repetitive and error-prone, especially when handling many characteristics and services. This can also lead to difficulty in maintaining the codebase, as any addition or modification requires updating multiple code segments.

example in code:
```c
// Manual GATT service and characteristic definitions

#define SERVICE_1_UUID 0x00FF
#define CHAR_1_UUID    0xFF01
#define CHAR_2_UUID    0xFF02

#define SERVICE_2_UUID 0x00FE
#define CHAR_3_UUID    0xFE01
#define CHAR_4_UUID    0xFE02

static uint8_t char1_value[20] = {0}; // Characteristic 1 value
static uint8_t char2_value[20] = {0}; // Characteristic 2 value
static uint8_t char3_value[20] = {0}; // Characteristic 3 value
static uint8_t char4_value[20] = {0}; // Characteristic 4 value

// GATT Server Event Handler
static void gatts_profile_event_handler(esp_gatts_cb_event_t event, esp_gatt_if_t gatts_if, esp_ble_gatts_cb_param_t *param) {
    switch (event) {
        case ESP_GATTS_REG_EVT:
            esp_ble_gatts_create_service(gatts_if, SERVICE_1_UUID, GATT_MAX_MTU_SIZE);
            esp_ble_gatts_create_service(gatts_if, SERVICE_2_UUID, GATT_MAX_MTU_SIZE);
            break;
        case ESP_GATTS_CREATE_EVT:
            if (param->create.service_id.id == SERVICE_1_UUID) {
                // Adding Characteristics for Service 1
                esp_ble_gatts_add_char(param->create.service_handle, CHAR_1_UUID,
                    ESP_GATT_PERM_READ | ESP_GATT_PERM_WRITE,
                    ESP_GATT_CHAR_PROP_READ | ESP_GATT_CHAR_PROP_WRITE,
                    &char1_value, NULL);
                esp_ble_gatts_add_char(param->create.service_handle, CHAR_2_UUID,
                    ESP_GATT_PERM_READ | ESP_GATT_PERM_WRITE,
                    ESP_GATT_CHAR_PROP_READ | ESP_GATT_CHAR_PROP_WRITE,
                    &char2_value, NULL);
            } else if (param->create.service_id.id == SERVICE_2_UUID) {
                // Adding Characteristics for Service 2
                esp_ble_gatts_add_char(param->create.service_handle, CHAR_3_UUID,
                    ESP_GATT_PERM_READ | ESP_GATT_PERM_WRITE,
                    ESP_GATT_CHAR_PROP_READ | ESP_GATT_CHAR_PROP_WRITE,
                    &char3_value, NULL);
                esp_ble_gatts_add_char(param->create.service_handle, CHAR_4_UUID,
                    ESP_GATT_PERM_READ | ESP_GATT_PERM_WRITE,
                    ESP_GATT_CHAR_PROP_READ | ESP_GATT_CHAR_PROP_WRITE,
                    &char4_value, NULL);
            }
            break;
        default:
            break;
    }
}
```
In the manual attribute-by-attribute approach, each GATT service and characteristic is defined and added individually, which can lead to verbose and repetitive code. This method involves explicitly creating each characteristic within the event handler, making the overall structure harder to read and maintain, especially as the number of services and characteristics increases. 

# 2. Attribute Table Approach:

In this approach, you define all attributes (services and characteristics) in a single array. This makes the code cleaner, more manageable, and easier to modify.

Key Differences:

Complexity: The manual approach requires the developer to manage every service, characteristic, and descriptor individually, leading to more complex and repetitive code. In contrast, the attribute table approach simplifies this by encapsulating all GATT attributes into a single, easy-to-manage table.

Ease of Use: The attribute table approach is generally more user-friendly, especially for developers new to BLE or those working with large, complex profiles. The manual approach offers more granular control but at the cost of increased complexity.

Flexibility: While the manual approach may be more flexible for custom setups, it is also more error-prone and difficult to maintain. The attribute table approach offers sufficient flexibility for most use cases while keeping the codebase organized and scalable.

Maintenance: With the manual approach, maintaining the code becomes harder as the GATT profile grows in complexity, requiring updates in multiple places for each new attribute. The attribute table approach, by contrast, offers a single point of definition, making maintenance more straightforward.

```c
// Attribute table GATT service and characteristic definitions

#define SERVICE_1_UUID 0x00FF
#define CHAR_1_UUID    0xFF01
#define CHAR_2_UUID    0xFF02

#define SERVICE_2_UUID 0x00FE
#define CHAR_3_UUID    0xFE01
#define CHAR_4_UUID    0xFE02

static uint8_t char1_value[20] = {0}; // Characteristic 1 value
static uint8_t char2_value[20] = {0}; // Characteristic 2 value
static uint8_t char3_value[20] = {0}; // Characteristic 3 value
static uint8_t char4_value[20] = {0}; // Characteristic 4 value

static const esp_gatts_attr_db_t gatt_db[] = {
    // Service 1 Declaration
    [0] = {
        {ESP_GATT_AUTO_RSP},
        {ESP_UUID_LEN_16, (uint8_t *)&primary_service_uuid, ESP_GATT_PERM_READ,
         sizeof(uint16_t), sizeof(SERVICE_1_UUID), (uint8_t *)&SERVICE_1_UUID}
    },
    // Characteristic 1 Declaration
    [1] = {
        {ESP_GATT_AUTO_RSP},
        {ESP_UUID_LEN_16, (uint8_t *)&character_declaration_uuid, ESP_GATT_PERM_READ,
         CHAR_DECLARATION_SIZE, CHAR_DECLARATION_SIZE, (uint8_t *)&char1_properties}
    },
    // Characteristic 1 Value
    [2] = {
        {ESP_GATT_AUTO_RSP},
        {ESP_UUID_LEN_16, (uint8_t *)&CHAR_1_UUID, ESP_GATT_PERM_READ | ESP_GATT_PERM_WRITE,
         sizeof(char1_value), sizeof(char1_value), (uint8_t *)&char1_value}
    },
    // Characteristic 2 Declaration
    [3] = {
        {ESP_GATT_AUTO_RSP},
        {ESP_UUID_LEN_16, (uint8_t *)&character_declaration_uuid, ESP_GATT_PERM_READ,
         CHAR_DECLARATION_SIZE, CHAR_DECLARATION_SIZE, (uint8_t *)&char2_properties}
    },
    // Characteristic 2 Value
    [4] = {
        {ESP_GATT_AUTO_RSP},
        {ESP_UUID_LEN_16, (uint8_t *)&CHAR_2_UUID, ESP_GATT_PERM_READ | ESP_GATT_PERM_WRITE,
         sizeof(char2_value), sizeof(char2_value), (uint8_t *)&char2_value}
    },
    // Service 2 Declaration
    [5] = {
        {ESP_GATT_AUTO_RSP},
        {ESP_UUID_LEN_16, (uint8_t *)&primary_service_uuid, ESP_GATT_PERM_READ,
         sizeof(uint16_t), sizeof(SERVICE_2_UUID), (uint8_t *)&SERVICE_2_UUID}
    },
    // Characteristic 3 Declaration
    [6] = {
        {ESP_GATT_AUTO_RSP},
        {ESP_UUID_LEN_16, (uint8_t *)&character_declaration_uuid, ESP_GATT_PERM_READ,
         CHAR_DECLARATION_SIZE, CHAR_DECLARATION_SIZE, (uint8_t *)&char3_properties}
    },
    // Characteristic 3 Value
    [7] = {
        {ESP_GATT_AUTO_RSP},
        {ESP_UUID_LEN_16, (uint8_t *)&CHAR_3_UUID, ESP_GATT_PERM_READ | ESP_GATT_PERM_WRITE,
         sizeof(char3_value), sizeof(char3_value), (uint8_t *)&char3_value}
    },
    // Characteristic 4 Declaration
    [8] = {
        {ESP_GATT_AUTO_RSP},
        {ESP_UUID_LEN_16, (uint8_t *)&character_declaration_uuid, ESP_GATT_PERM_READ,
         CHAR_DECLARATION_SIZE, CHAR_DECLARATION_SIZE, (uint8_t *)&char4_properties}
    },
    // Characteristic 4 Value
    [9] = {
        {ESP_GATT_AUTO_RSP},
        {ESP_UUID_LEN_16, (uint8_t *)&CHAR_4_UUID, ESP_GATT_PERM_READ | ESP_GATT_PERM_WRITE,
         sizeof(char4_value), sizeof(char4_value), (uint8_t *)&char4_value}
    },
};

// GATT Server Event Handler
static void gatts_profile_event_handler(esp_gatts_cb_event_t event, esp_gatt_if_t gatts_if, esp_ble_gatts_cb_param_t *param) {
    switch (event) {
        case ESP_GATTS_REG_EVT:
            esp_ble_gatts_create_service(gatts_if, SERVICE_1_UUID, GATT_MAX_MTU_SIZE);
            esp_ble_gatts_create_service(gatts_if, SERVICE_2_UUID, GATT_MAX_MTU_SIZE);
            break;
        case ESP_GATTS_CREATE_EVT:
            // Adding all attributes in one go using the defined table
            esp_ble_gatts_add_attr_tab(gatt_db, param->create.service_handle, sizeof(gatt_db) / sizeof(gatt_db[0]));
            break;
        default:
            break;
    }
}
```

The attribute table approach consolidates all service and characteristic definitions into a single array, streamlining the process of defining multiple attributes at once. This method enhances readability and maintainability by clearly organizing all attributes in one place, allowing for easier scaling and modifications, ultimately resulting in cleaner and more concise code.
