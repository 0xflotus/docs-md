---
kind: InterfaceDeclaration
name: EntityCollectionService
module: data
---

# EntityCollectionService

## description

A facade for managing
a cached collection of T entities in the ngrx store.

```ts
interface EntityCollectionService<T> {
  readonly dispatcher: EntityDispatcher<T>;
  readonly entityName: string;
  readonly selectors: EntitySelectors<T>;
  readonly selectors$: EntitySelectors$<T>;

  // inherited from EntityCommands

  // inherited from EntitySelectors$
  readonly entityName: string;
  readonly collection$: Observable<EntityCollection> | Store<EntityCollection>;
  readonly count$: Observable<number> | Store<number>;
  readonly entities$: Observable<T[]> | Store<T[]>;
  readonly entityActions$: Observable<EntityAction>;
  readonly entityMap$: Observable<Dictionary<T>> | Store<Dictionary<T>>;
  readonly errors$: Observable<EntityAction>;
  readonly filter$: Observable<string> | Store<string>;
  readonly filteredEntities$: Observable<T[]> | Store<T[]>;
  readonly keys$: Observable<string[] | number[]> | Store<string[] | number[]>;
  readonly loaded$: Observable<boolean> | Store<boolean>;
  readonly loading$: Observable<boolean> | Store<boolean>;
  readonly changeState$:
    | Observable<ChangeStateMap<T>>
    | Store<ChangeStateMap<T>>;
}
```

[Link to repo](https://github.com/ngrx/platform/blob/master/modules/data/src/entity-services/entity-collection-service.ts#L14-L55)

## Properties

| Name        | Type                  | Description                                                                    |
| ----------- | --------------------- | ------------------------------------------------------------------------------ |
| dispatcher  | `EntityDispatcher<T>` | Dispatcher of EntityCommands (EntityActions)                                   |
| entityName  | `string`              | Name of the entity type for this collection service                            |
| selectors   | `EntitySelectors<T>`  | All selector functions of the entity collection                                |
| selectors\$ | `EntitySelectors$<T>` | All selectors\$ (observables of the selectors of entity collection properties) |
