
# ER Diagram: Product & Barcode Relationship

```mermaid
erDiagram

    product {
        bigint id PK "auto increment"
        string(100) sku_id "Internal SKU code (unique per item)"
        string(255) sku_name "Descriptive name"
        string(50) uom "Unit of measure (e.g., EA, BOX)"
        string(50) category
        string(50) brand
        datetime created_at
        datetime updated_at
    }

    product_barcode {
        bigint id PK "auto increment"
        bigint product_id FK "References product.id"
        string(50) barcode "Physical barcode (EAN, UPC, internal)"
        string(50) uom "Unit for this barcode (e.g., EA, PACK, CASE)"
        boolean is_default "Indicate main barcode"
        string(50) packaging_type "e.g., SINGLE, INNER, OUTER"
        datetime created_at
        datetime updated_at
    }

    product ||--o{ product_barcode : "has"
```

**Explanation**

| Table               | Description                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **product**         | Represents the **SKU master** — one row per logical item type (e.g., “Coca-Cola 330ml Can”).                        |
| **product_barcode** | Represents the **physical identification** of how that SKU appears in the warehouse (e.g., 1 can, 6-pack, 24-pack). |


### Optional Enhancements

```mermaid
erDiagram

    product_barcode {
        bigint id PK
        bigint product_id FK
        string barcode
        string uom
        string packaging_type
        boolean is_default
        string source_type "SUPPLIER | INTERNAL | CUSTOMER"
        string source_code "VendorCode or CustomerCode"
    }

    product ||--o{ product_barcode : "has"

```