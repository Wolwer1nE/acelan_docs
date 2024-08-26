## Задачи в пакете ACELAN

Для решения пользователем задач в пакете ACELAN реализован класс ```ProblemSolver```.
От пользователя приходит строка в формате JSON, которая содержит информацию о скрипте ("ScriptInfo") пользователя и данные для его выполнения. 
Для сериализации данных этой строки используется класс ```ScriptTask```. 
После сериализации решается соответствующая "taskName" задача.

Класс ```ScriptTask``` содержит в себе информацию о скрипте ("ScriptInfo") и данные для всех типов задач. 
Необходимые для конкретной задачи данные выбираются в зависимости от имени задачи.

```json
"ScriptInfo": {
  "session": "tmp1",
  "taskName": "my task",
  "taskType": "OptimizationProblem"
}
```

Реализованы 4 типа задач:
- Прямая задача
- Идентификация материала
- Топологическая оптимизация
- Собственные значения модели

Результатом решения задач является строка в формате JAM. Для десериализации используется класс Jam, а для формирования различных JAM используется класс ```JamBuilder```.

#### JAM
```json
{
  "properties": {
    "elementType": "Triangle"
  },
  "nodes": [
    {
      "id": 0,
      "x": 0.0,
      "y": 0.0,
      "z": 0.0
    }
  ],
  "elements": [
    {
      "id": 0,
      "nodes": [
        2,
        1,
        0
      ],
      "body": 0
    }
  ],
  "nodalSolution": {
    "ux": [],
    "uy": [],
    "uz": [],
    "phi": []
  },
  "elementalSolution": {
    "eps11": [],
    "eps22": [],
    "eps12": [],
    "epsT": [],
    "sigma11": [],
    "sigma22": [],
    "sigma12": [],
    "sigmaT": []
  },
  "metaData": {
    "version": "0.1.1",
    "status": "Success",
    "message": "",
    "signature": "123"
  }
}
```

#### Формат описания материала
```json
{
      "name": "PZT",
      "density": 7500,
      "c": [
        [1.38999e+011, 7.78366e+010, 7.42836e+010, 0,  0, 0],
        [7.78366e+010, 1.38999e+011, 7.42836e+010, 0,  0, 0],
        [7.42836e+010, 7.42836e+010, 1.15412e+011, 0,  0, 0],
        [0, 0, 0, 2.5641e+010, 0, 0],
        [0, 0, 0, 0, 2.5641e+010, 0],
        [0, 0, 0, 0, 0, 3.0581e+010]
      ],
      "g": [
        [1475, 0, 0],
        [0, 1475, 0],
        [0, 0, 1300]
      ],
      "e": [
        [0, 0, 0, 0, 12.7179, 0],
        [0, 0, 0, 12.7179, 0, 0],
        [-5.20279, -5.20279, 15.0804, 0, 0, 0]
      ]
    }
```

#### Формат описания модели
```json
"DataModel": {
  "mesh": "DataForTests/static_hex.nas",
  "materials": [],
  "boundaryConditions": [ "z = 0, ux = 0;\n z = 0, uy = 0;\n z = 0, uz = 0;\n PointLoad3(0, 0, 0.01, 0, 0, -1000);" ],
  "variables": ":ux, :uy, :uz",
  "elementType": ":hex"
}
```

## Задачи:
### 1. Прямая задача

```
mesh = "http://127.0.0.1:5200/artifacts/block_mesh.nas"
materials = load_materials('default.db')
material = materials.get('PMM')
element = build_element('Hex8', 'U, Phi, Psi')
body = build_body(material, element)
boundary_conditions = ["x = 0 => ux = 0", 
                       "y = 0 => uy = 0", 
                       "z = 0 => uz = 0" ,
                       "z = 0 => phi = 0",
                       "z = 0 => psi = 0",
                       "z = 1=> phi = 100"]
data_source = OctreeDataSource.new(2) 
model = build_model(data_source, [body], [boundary_conditions]) 
solution = model.solve(CSparseLU)
```

```json
{
  "ScriptInfo": {
    "session": "tmp1",
    "taskName": "my task",
    "taskType": "IdentifierProblem"
  },
  "DataModel": {
    "mesh": "DataForTests/static_hex.nas",
    "materials": [],
    "boundaryConditions": [ "z = 0, ux = 0;\n z = 0, uy = 0;\n z = 0, uz = 0;\n PointLoad3(0, 0, 0.01, 0, 0, -1000);" ],
    "variables": ":ux, :uy, :uz",
    "elementType": ":hex"
  }
}
```

```json
{
  "properties": {
    "elementType": "Triangle"
  },
  "nodes": [
    {
      "id": 0,
      "x": 0.0,
      "y": 0.0,
      "z": 0.0
    }
  ],
  "elements": [
    {
      "id": 0,
      "nodes": [
        2,
        1,
        0
      ],
      "body": 0
    }
  ],
  "material": { },
  "nodalSolution": {
    "ux": [],
    "uy": [],
    "uz": [],
    "phi": []
  },
  "elementalSolution": {
    "eps11": [],
    "eps22": [],
    "eps12": [],
    "sigma11": [],
    "sigma22": [],
    "sigma12": [],
  },
  "metaData": {
    "version": "0.1.1",
    "status": "Success",
    "message": "",
    "signature": "123"
  }
}
```

### 2. Идентификация материала

mode = [fast, normal, precise]

```
materials = load_materials('default.db')
material_1 = materials.get('PMM')
material_2 = materials.get('PMM')
method = '3-0'
mode = 'fast'
solution = material_identifier(material1, material2, method, mode, porosity)
```

```json
{
  "ScriptInfo": {
    "session": "tmp1",
    "taskName": "my task",
    "taskType": "IdentifierProblem"
  },

  "DataIdentifierProblem": {
    "material1": {},
    "material2": {},
    "mode": "Faster",
    "methodIdentifier": "ThreeOneConverter",
    "porosity": 50,
    "homogenisationTarget": "Elastic"
  }
}
```

```json
{
  "properties": {
    "elementType": "Triangle"
  },
  "nodes": [
    {
      "id": 0,
      "x": 0.0,
      "y": 0.0,
      "z": 0.0
    }
  ],
  "elements": [
    {
      "id": 0,
      "nodes": [
        2,
        1,
        0
      ],
      "body": 0
    }
  ],
  "material": { },
  "metaData": {
    "version": "0.1.1",
    "status": "Success",
    "message": "",
    "signature": "123"
  }
}
```

### 3. Топологическая оптимизация

```
mesh = "http://127.0.0.1:5200/artifacts/block_mesh.nas"
materials = load_materials('default.db')
material = materials.get('PMM')
element = build_element('Hex8', 'U, Phi, Psi')
body = build_body(material, element)
boundary_conditions = ["x = 0 => ux = 0", 
                       "y = 0 => uy = 0", 
                       "z = 0 => uz = 0" ,
                       "z = 0 => phi = 0",
                       "z = 0 => psi = 0",
                       "z = 1=> phi = 100"]
data_source = OctreeDataSource.new(2) 
model = build_model(data_source, [body], [boundary_conditions]) 
methodOptimization = 'ESO'
percentageOptimization = 50
senseName = 'AverageStressSense'
solution = model.topology_optimization(method, percentage)
```

```json
{
  "ScriptInfo": {
    "session": "tmp1",
    "taskName": "my task",
    "taskType": "OptimizationProblem"
  },
  "DataOptimizationProblem": {
    "DataModel": {
      "mesh": "DataForTests/small_top_opt_mesh.nas",
      "materials": [],
      "boundaryConditions": [ "x = 0, ux = 0;\n x = 0, uy = 0;\n x = 0, uz = 0;\n  x = 0.016, uz = 0.0001;" ],
      "variables": ":ux, :uy, :uz",
      "elementType": ":hex"
    },
    "materialToRemoveIndex": 1,
    "methodOptimization": "BESO",
    "percentageOptimization": 0.5,
    "senseName": "AverageStressSense"
  }
  }
```

```json
{
  "properties": {
    "elementType": "Triangle"
  },
  "nodes": [
    {
      "id": 0,
      "x": 0.0,
      "y": 0.0,
      "z": 0.0
    }
  ],
  "elements": [
    {
      "id": 0,
      "nodes": [
        2,
        1,
        0
      ],
      "body": 0
    }
  ],
  "metaData": {
    "version": "0.1.1",
    "status": "Success",
    "message": "",
    "signature": "123"
  }
}
```

### 4. Собственные значения модели

```
mesh = "http://127.0.0.1:5200/artifacts/block_mesh.nas"
materials = load_materials('default.db')
material = materials.get('PMM')
element = build_element('Hex8', 'U, Phi, Psi')
body = build_body(material, element)
boundary_conditions = ["x = 0 => ux = 0", 
                       "y = 0 => uy = 0", 
                       "z = 0 => uz = 0" ,
                       "z = 0 => phi = 0",
                       "z = 0 => psi = 0",
                       "z = 1=> phi = 100"]
data_source = OctreeDataSource.new(2) 
model = build_model(data_source, [body], [boundary_conditions]) 
count = 5
around = 1000
solution = model.eigenfrequency(count, around)
```

```json
{
  "session": "tmp1",
  "taskName": "my task",
  "type": "EigenProblem",
  "data": {
    "mesh": "http://127.0.0.1:5200/artifacts/block_mesh.nas",
    "material": {},
    "boundaryConditions": "x = 0, ux = 0;\n y = 0, uy = 0;\n PointLoad(0.016,0.005,0,-1000)",
    "variables": ":ux, :uy",
    "elementType": ":triangle",
    "around": 1000,
    "count": 5
  },
  "callbackUrl": "http://127.0.0.1:3000/api/reports/"
}
```

```json
{
  "properties": {
    "elementType": "Triangle"
  },
  "nodes": [
    {
      "id": 0,
      "x": 0.0,
      "y": 0.0,
      "z": 0.0
    }
  ],
  "elements": [
    {
      "id": 0,
      "nodes": [
        2,
        1,
        0
      ],
      "body": 0
    }
  ],
  "eigenFrequencies": [
    {
      "freq": 1000,
      "nodalSolution": {
        "ux": [],
        "uy": [],
        "uz": [],
        "phi": []
      },
      "elementalSolution": {
        "eps11": [],
        "eps22": [],
        "eps12": [],
        "epsT": [],
        "sigma11": [],
        "sigma22": [],
        "sigma12": [],
        "sigmaT": []
      },
      "residual": 1e-8
    }
  ],
  "metaData": {
    "version": "0.1.1",
    "status": "Success",
    "message": "",
    "signature": "123"
  }
}
```
