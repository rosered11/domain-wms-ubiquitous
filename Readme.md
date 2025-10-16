# Domain WMS Ubiquitous

### SkuId (Stock Keeping Unit ID)

**Definition:**
A unique internal identifier for a distinct product or item type managed in your system.

| Attribute       | Description                                                                                     |
| --------------- | ----------------------------------------------------------------------------------------------- |
| **Level**       | Logical / system level                                                                          |
| **Purpose**     | Used internally by the system to identify a *product definition*                                |
| **Uniqueness**  | Unique per product variant (e.g. color, size, pack)                                             |
| **Assigned By** | Typically assigned by your ERP or product master system                                         |
| **Example**     | `SKU12345` for *"Coca-Cola 330ml Can"*                                                          |
| **Usage**       | - Inventory management<br>- Order picking<br>- Stock reconciliation<br>- Product master mapping |

In short: `SkuId = What the item is (the system’s internal identifier for a product).`

### Barcode

Definition:
A physical code printed on the product or packaging, which represents an item that can be scanned (e.g., during receiving, picking, or shipping).

**Characteristics:**

| Attribute       | Description                                                                            |
| --------------- | -------------------------------------------------------------------------------------- |
| **Level**       | Physical / packaging level                                                             |
| **Purpose**     | Used to identify an item when scanned (at runtime)                                     |
| **Uniqueness**  | May not be unique per SKU — depends on packaging                                       |
| **Assigned By** | Manufacturer or your warehouse labeling process                                        |
| **Example**     | `8851959132214` (EAN-13 for a single can), or `8851959132215` (for a 6-pack)           |
| **Usage**       | - Scanning during inbound/outbound<br>- Label printing<br>- Physical handling of goods |

In short: `Barcode = How the item is recognized physically (the machine-readable representation).`

### Relationship between SkuId and Barcode

| Scenario      | Description                                                                                |
| ------------- | ------------------------------------------------------------------------------------------ |
| **1-to-1**    | One SKU has one barcode (simple products).                                                 |
| **1-to-many** | One SKU has multiple barcodes (e.g., different pack sizes, internal vs. supplier barcode). |
| **Many-to-1** | Rare, but can happen if multiple SKUs share a common barcode (e.g., generic items).        |

**Example**

```
SkuId: SKU12345 (Coca-Cola 330ml)
 ├── Barcode: 8851959132214 (Single can)
 ├── Barcode: 8851959132215 (6-pack)
 └── Barcode: 8851959132216 (24-pack)
```

### WMS Domain Model

```
Product
 ├── SkuId (PK)
 ├── SkuName
 ├── UOM
 └── other master data

ProductBarcode
 ├── Id (PK)
 ├── SkuId (FK)
 ├── Barcode
 ├── UOM
 ├── IsDefault
 └── PackagingType
```

### Summary

| Concept      | Represents                                             | Scope                | Example                  |
| ------------ | ------------------------------------------------------ | -------------------- | ------------------------ |
| **SkuId**    | Logical product identity                               | Internal/system      | SKU12345                 |
| **Barcode**  | Physical code on packaging                             | Physical/operational | 8851959132214            |
| **Relation** | 1 SKU → many barcodes (for pack variants or suppliers) | Master data link     | via ProductBarcode table |


**Example Data**

| product.id | sku_id   | sku_name        | uom |
| ---------- | -------- | --------------- | --- |
| 1          | SKU12345 | Coca-Cola 330ml | EA  |
| 2          | SKU12346 | Pepsi 500ml     | EA  |


| product_barcode.id | product_id | barcode       | uom  | packaging_type | is_default |
| ------------------ | ---------- | ------------- | ---- | -------------- | ---------- |
| 1                  | 1          | 8851959132214 | EA   | SINGLE         | ✅          |
| 2                  | 1          | 8851959132215 | PACK | 6-PACK         | ❌          |
| 3                  | 1          | 8851959132216 | CASE | 24-PACK        | ❌          |
| 4                  | 2          | 8851123456789 | EA   | SINGLE         | ✅          |


### Summary — Inbound vs Outbound

| Flow         | Starting Point | Key Operation     | Barcode Use      | Inventory Movement |
| ------------ | -------------- | ----------------- | ---------------- | ------------------ |
| **Inbound**  | ASN / PO       | Receive & Putaway | ✅ Scan items in  | Increase (IN)      |
| **Outbound** | Sales Order    | Pick & Ship       | ✅ Scan items out | Decrease (OUT)     |

### Key Takeaways

- SkuId → logical/system-level identity (ERP/WMS integration key).

- Barcode → physical scan identity used in operations (receiving, putaway, picking).

- Both flows (inbound & outbound) meet at inventory_transaction, ensuring full traceability.