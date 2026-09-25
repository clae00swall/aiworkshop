# ABL Business Entity Architecture Pattern

This is a condensed overview of the pattern demonstrated by the Step-16 ProjectGengar code. The source document in ProjectGengar contains the full examples and refactoring guide.

## Layers

- UI (`src/CustomerWin.w`) handles presentation and passes datasets to business entities.
- Business entities (`src/business/CustomerEntity.cls`) inherit `OpenEdge.BusinessLogic.BusinessEntity` and implement data access, validation, and CRUD methods.
- Persistent storage is accessed through the entities' ABL data sources.

## Components

- `src/business/CustomerDataset.i` defines `ttCustomer` with a before-table and the `dsCustomer` dataset.
- `src/business/CustomerEntity.cls` passes `DATASET dsCustomer:HANDLE` to `SUPER`, then configures its `ProDataSource` and `SkipList` properties. `ReadData`, `CreateData`, `UpdateData`, and `DeleteData` perform the database operations.
- `src/business/EntityFactory.cls` lazily creates a singleton factory and customer entity.
- The UI requests an entity from the factory, calls methods with `dsCustomer`, and renders the returned temp-table contents.

## Reading and writing

- Queries use an entity method to call `ReadData` with a filter and return an output dataset.
- Create, update, and delete operations pass the dataset by reference to the entity's parent-class operations.
- For updates, enable `TEMP-TABLE ttCustomer:TRACKING-CHANGES` before modifying the record so the before-table captures changes.
- Validate before saving. Define locally scoped, named buffers for direct database table access.

## Refactoring legacy UI code

Identify UI code that reads or writes database records directly, define a dataset, create a business entity and register it with the factory, move data access and validation into the entity, and test reads, updates, and invalid input incrementally.

The original Step-16 source contains further examples for multi-table entities, UI integration, common pitfalls, and a detailed step-by-step guide.
