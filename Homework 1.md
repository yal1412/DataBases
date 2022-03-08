# ДЗ1

## Установка и загрузка данных

Установили MongoDB и загрузили csv датасет в коллекцию.

![Untitled](%D0%94%D0%971%2072265/Untitled.png)

Вставили сначала один объект, потом несколько.

![Untitled](%D0%94%D0%971%2072265/Untitled%201.png)

## Выборки и обновления данных

Вывели всех покупателей возрастом 25 лет.

![Untitled](%D0%94%D0%971%2072265/Untitled%202.png)

Вывели всех покупателей женщин возрастом 25 лет.

![Untitled](%D0%94%D0%971%2072265/Untitled%203.png)

Покупатели возрастом 25 лет или с доходом меньше 17.

![Untitled](%D0%94%D0%971%2072265/Untitled%204.png)

Покупатели женщины 25 лет, отсортированные по доходу.

![Untitled](%D0%94%D0%971%2072265/Untitled%205.png)

Обновим один объект.

После обновления:

![Untitled](%D0%94%D0%971%2072265/Untitled%206.png)

![Untitled](%D0%94%D0%971%2072265/Untitled%207.png)

Переименуем поле "Annual Income (k$)" в Income.

![Untitled](%D0%94%D0%971%2072265/Untitled%208.png)

Добавим покупателя и удалим другого с помощью одной операции.

![Untitled](%D0%94%D0%971%2072265/Untitled%209.png)

## Использование индексов и сравнение производительности

Для сравнения производительности взяла другой датасет на 1355730 строк ([Ссылка на датасет](https://www.kaggle.com/c/departure-delay/data)).

Пример объекта из этого датасета - данные о полёте самолёта.

![Untitled](%D0%94%D0%971%2072265/Untitled%2010.png)

Выведем информацию по поиску всех рейсов до 15 января включительно.

```jsx
db.DepartureDelay.find({Month:1, DayOfMonth:{$lte:15}}).explain("executionStats")

{ explainVersion: '1',
  queryPlanner: 
   { namespace: 'MallCustomers.DepartureDelay',
     indexFilterSet: false,
     parsedQuery: { '$and': [ { Month: { '$eq': 1 } }, { DayOfMonth: { '$lte': 15 } } ] },
     maxIndexedOrSolutionsReached: false,
     maxIndexedAndSolutionsReached: false,
     maxScansToExplodeReached: false,
     winningPlan: 
      { stage: 'COLLSCAN',
        filter: { '$and': [ { Month: { '$eq': 1 } }, { DayOfMonth: { '$lte': 15 } } ] },
        direction: 'forward' },
     rejectedPlans: [] },
  executionStats: 
   { executionSuccess: true,
     nReturned: 0,
     executionTimeMillis: 1045,
     totalKeysExamined: 0,
     totalDocsExamined: 1355730,
     executionStages: 
      { stage: 'COLLSCAN',
        filter: { '$and': [ { Month: { '$eq': 1 } }, { DayOfMonth: { '$lte': 15 } } ] },
        nReturned: 0,
        executionTimeMillisEstimate: 50,
        works: 1355732,
        advanced: 0,
        needTime: 1355731,
        needYield: 0,
        saveState: 1355,
        restoreState: 1355,
        isEOF: 1,
        direction: 'forward',
        docsExamined: 1355730 } },
  command: 
   { find: 'DepartureDelay',
     filter: { Month: 1, DayOfMonth: { '$lte': 15 } },
     '$db': 'MallCustomers' },
  serverInfo: 
   { host: 'LAPTOP-OB70NTCE',
     port: 27017,
     version: '5.0.6',
     gitVersion: '212a8dbb47f07427dae194a9c75baec1d81d9259' },
  serverParameters: 
   { internalQueryFacetBufferSizeBytes: 104857600,
     internalQueryFacetMaxOutputDocSizeBytes: 104857600,
     internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
     internalDocumentSourceGroupMaxMemoryBytes: 104857600,
     internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
     internalQueryProhibitBlockingMergeOnMongoS: 0,
     internalQueryMaxAddToSetBytes: 104857600,
     internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600 },
  ok: 1 }
```

Создали индексы и еще раз запустили ту же функцию.

![Untitled](%D0%94%D0%971%2072265/Untitled%2011.png)

```jsx
db.DepartureDelay.find({Month:1, DayOfMonth:{$lte:15}}).hint({ Month: 1, DayOfMonth: 1 }).explain("executionStats")

{ explainVersion: '1',
  queryPlanner: 
   { namespace: 'MallCustomers.DepartureDelay',
     indexFilterSet: false,
     parsedQuery: { '$and': [ { Month: { '$eq': 1 } }, { DayOfMonth: { '$lte': 15 } } ] },
     maxIndexedOrSolutionsReached: false,
     maxIndexedAndSolutionsReached: false,
     maxScansToExplodeReached: false,
     winningPlan: 
      { stage: 'FETCH',
        inputStage: 
         { stage: 'IXSCAN',
           keyPattern: { Month: 1, DayOfMonth: 1 },
           indexName: 'Month_1_DayOfMonth_1',
           isMultiKey: false,
           multiKeyPaths: { Month: [], DayOfMonth: [] },
           isUnique: false,
           isSparse: false,
           isPartial: false,
           indexVersion: 2,
           direction: 'forward',
           indexBounds: { Month: [ '[1, 1]' ], DayOfMonth: [ '[-inf.0, 15]' ] } } },
     rejectedPlans: [] },
  executionStats: 
   { executionSuccess: true,
     nReturned: 0,
     executionTimeMillis: 1,
     totalKeysExamined: 0,
     totalDocsExamined: 0,
     executionStages: 
      { stage: 'FETCH',
        nReturned: 0,
        executionTimeMillisEstimate: 0,
        works: 1,
        advanced: 0,
        needTime: 0,
        needYield: 0,
        saveState: 0,
        restoreState: 0,
        isEOF: 1,
        docsExamined: 0,
        alreadyHasObj: 0,
        inputStage: 
         { stage: 'IXSCAN',
           nReturned: 0,
           executionTimeMillisEstimate: 0,
           works: 1,
           advanced: 0,
           needTime: 0,
           needYield: 0,
           saveState: 0,
           restoreState: 0,
           isEOF: 1,
           keyPattern: { Month: 1, DayOfMonth: 1 },
           indexName: 'Month_1_DayOfMonth_1',
           isMultiKey: false,
           multiKeyPaths: { Month: [], DayOfMonth: [] },
           isUnique: false,
           isSparse: false,
           isPartial: false,
           indexVersion: 2,
           direction: 'forward',
           indexBounds: { Month: [ '[1, 1]' ], DayOfMonth: [ '[-inf.0, 15]' ] },
           keysExamined: 0,
           seeks: 1,
           dupsTested: 0,
           dupsDropped: 0 } } },
  command: 
   { find: 'DepartureDelay',
     filter: { Month: 1, DayOfMonth: { '$lte': 15 } },
     hint: { Month: 1, DayOfMonth: 1 },
     '$db': 'MallCustomers' },
  serverInfo: 
   { host: 'LAPTOP-OB70NTCE',
     port: 27017,
     version: '5.0.6',
     gitVersion: '212a8dbb47f07427dae194a9c75baec1d81d9259' },
  serverParameters: 
   { internalQueryFacetBufferSizeBytes: 104857600,
     internalQueryFacetMaxOutputDocSizeBytes: 104857600,
     internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
     internalDocumentSourceGroupMaxMemoryBytes: 104857600,
     internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
     internalQueryProhibitBlockingMergeOnMongoS: 0,
     internalQueryMaxAddToSetBytes: 104857600,
     internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600 },
  ok: 1 }
```

И ещё один эксперимент.

Без индексов.

```jsx
db.DepartureDelay.find({DayOfWeek:{$lte:4}, DayOfMonth:{$lte:15}}).explain("executionStats")

{ explainVersion: '1',
  queryPlanner: 
   { namespace: 'MallCustomers.DepartureDelay',
     indexFilterSet: false,
     parsedQuery: { '$and': [ { DayOfMonth: { '$lte': 15 } }, { DayOfWeek: { '$lte': 4 } } ] },
     maxIndexedOrSolutionsReached: false,
     maxIndexedAndSolutionsReached: false,
     maxScansToExplodeReached: false,
     winningPlan: 
      { stage: 'COLLSCAN',
        filter: { '$and': [ { DayOfMonth: { '$lte': 15 } }, { DayOfWeek: { '$lte': 4 } } ] },
        direction: 'forward' },
     rejectedPlans: [] },
  executionStats: 
   { executionSuccess: true,
     nReturned: 0,
     executionTimeMillis: 1011,
     totalKeysExamined: 0,
     totalDocsExamined: 1355730,
     executionStages: 
      { stage: 'COLLSCAN',
        filter: { '$and': [ { DayOfMonth: { '$lte': 15 } }, { DayOfWeek: { '$lte': 4 } } ] },
        nReturned: 0,
        executionTimeMillisEstimate: 66,
        works: 1355732,
        advanced: 0,
        needTime: 1355731,
        needYield: 0,
        saveState: 1355,
        restoreState: 1355,
        isEOF: 1,
        direction: 'forward',
        docsExamined: 1355730 } },
  command: 
   { find: 'DepartureDelay',
     filter: { DayOfWeek: { '$lte': 4 }, DayOfMonth: { '$lte': 15 } },
     '$db': 'MallCustomers' },
  serverInfo: 
   { host: 'LAPTOP-OB70NTCE',
     port: 27017,
     version: '5.0.6',
     gitVersion: '212a8dbb47f07427dae194a9c75baec1d81d9259' },
  serverParameters: 
   { internalQueryFacetBufferSizeBytes: 104857600,
     internalQueryFacetMaxOutputDocSizeBytes: 104857600,
     internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
     internalDocumentSourceGroupMaxMemoryBytes: 104857600,
     internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
     internalQueryProhibitBlockingMergeOnMongoS: 0,
     internalQueryMaxAddToSetBytes: 104857600,
     internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600 },
  ok: 1 }
```

С индексами

```jsx
db.DepartureDelay.find({DayOfWeek:{$lte:4}, DayOfMonth:{$lte:15}}).hint({ DayOfWeek: 1, DayOfMonth: 1 }).explain("executionStats")
{ explainVersion: '1',
  queryPlanner: 
   { namespace: 'MallCustomers.DepartureDelay',
     indexFilterSet: false,
     parsedQuery: { '$and': [ { DayOfMonth: { '$lte': 15 } }, { DayOfWeek: { '$lte': 4 } } ] },
     maxIndexedOrSolutionsReached: false,
     maxIndexedAndSolutionsReached: false,
     maxScansToExplodeReached: false,
     winningPlan: 
      { stage: 'FETCH',
        inputStage: 
         { stage: 'IXSCAN',
           keyPattern: { DayOfWeek: 1, DayOfMonth: 1 },
           indexName: 'DayOfWeek_1_DayOfMonth_1',
           isMultiKey: false,
           multiKeyPaths: { DayOfWeek: [], DayOfMonth: [] },
           isUnique: false,
           isSparse: false,
           isPartial: false,
           indexVersion: 2,
           direction: 'forward',
           indexBounds: { DayOfWeek: [ '[-inf.0, 4]' ], DayOfMonth: [ '[-inf.0, 15]' ] } } },
     rejectedPlans: [] },
  executionStats: 
   { executionSuccess: true,
     nReturned: 0,
     executionTimeMillis: 1,
     totalKeysExamined: 5,
     totalDocsExamined: 0,
     executionStages: 
      { stage: 'FETCH',
        nReturned: 0,
        executionTimeMillisEstimate: 0,
        works: 5,
        advanced: 0,
        needTime: 4,
        needYield: 0,
        saveState: 0,
        restoreState: 0,
        isEOF: 1,
        docsExamined: 0,
        alreadyHasObj: 0,
        inputStage: 
         { stage: 'IXSCAN',
           nReturned: 0,
           executionTimeMillisEstimate: 0,
           works: 5,
           advanced: 0,
           needTime: 4,
           needYield: 0,
           saveState: 0,
           restoreState: 0,
           isEOF: 1,
           keyPattern: { DayOfWeek: 1, DayOfMonth: 1 },
           indexName: 'DayOfWeek_1_DayOfMonth_1',
           isMultiKey: false,
           multiKeyPaths: { DayOfWeek: [], DayOfMonth: [] },
           isUnique: false,
           isSparse: false,
           isPartial: false,
           indexVersion: 2,
           direction: 'forward',
           indexBounds: { DayOfWeek: [ '[-inf.0, 4]' ], DayOfMonth: [ '[-inf.0, 15]' ] },
           keysExamined: 5,
           seeks: 5,
           dupsTested: 0,
           dupsDropped: 0 } } },
  command: 
   { find: 'DepartureDelay',
     filter: { DayOfWeek: { '$lte': 4 }, DayOfMonth: { '$lte': 15 } },
     hint: { DayOfWeek: 1, DayOfMonth: 1 },
     '$db': 'MallCustomers' },
  serverInfo: 
   { host: 'LAPTOP-OB70NTCE',
     port: 27017,
     version: '5.0.6',
     gitVersion: '212a8dbb47f07427dae194a9c75baec1d81d9259' },
  serverParameters: 
   { internalQueryFacetBufferSizeBytes: 104857600,
     internalQueryFacetMaxOutputDocSizeBytes: 104857600,
     internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
     internalDocumentSourceGroupMaxMemoryBytes: 104857600,
     internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
     internalQueryProhibitBlockingMergeOnMongoS: 0,
     internalQueryMaxAddToSetBytes: 104857600,
     internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600 },
  ok: 1 }
```

Увидели, что индексы очень сильно ускоряют обработку запросов.