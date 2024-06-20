## Задачи в пакете ACELAN

### Формат описания материалов

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


### Прямая задача

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
  "session": "tmp1",
  "taskName": "my task",
  "type": "solve",
  "data": {
    "mesh": "http://127.0.0.1:5200/artifacts/block_mesh.nas",
    "material": { },
    "boundaryConditions": "x = 0, ux = 0;\n y = 0, uy = 0;\n PointLoad(0.016,0.005,0,-1000)",
    "variables": ":ux, :uy",
    "elementType": ":triangle"
  },
  "callback_url": "http://127.0.0.1:3000/api/reports/"
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

### Идентификация материала

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
  "session": "tmp1",
  "task_name": "my task",
  "type": "material_identifier",
  "data": {
    "material_1": {},
    "material_2": {
      "name": "PZT",
      "density": 7500,
      "c": [
        [
          1.38999e+011,
          7.78366e+010,
          7.42836e+010,
          0,
          0,
          0
        ],
        [
          7.78366e+010,
          1.38999e+011,
          7.42836e+010,
          0,
          0,
          0
        ],
        [
          7.42836e+010,
          7.42836e+010,
          1.15412e+011,
          0,
          0,
          0
        ],
        [
          0,
          0,
          0,
          2.5641e+010,
          0,
          0
        ],
        [
          0,
          0,
          0,
          0,
          2.5641e+010,
          0
        ],
        [
          0,
          0,
          0,
          0,
          0,
          3.0581e+010
        ]
      ],
      "g": [
        [
          1475,
          0,
          0
        ],
        [
          0,
          1475,
          0
        ],
        [
          0,
          0,
          1300
        ]
      ],
      "e": [
        [
          0,
          0,
          0,
          0,
          12.7179,
          0
        ],
        [
          0,
          0,
          0,
          12.7179,
          0,
          0
        ],
        [
          -5.20279,
          -5.20279,
          15.0804,
          0,
          0,
          0
        ]
      ]
    },
    "method": "3-0",
    "mode": "fast",
    "porosity": 50
  },
  "callback_url": "http://127.0.0.1:3000/api/reports/"
}
```

```json
{
  "material": { },
  "metaData": {
    "version": "0.1.1",
    "status": "Success",
    "message": "",
    "signature": "123"
  }
}
```

### Топологическая оптиимизация

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
method = 'ESO'
percentage = 50
solution = model.topology_optimization(method, percentage)
```

```json
{
  "session": "tmp1",
  "taskName": "my task",
  "type": "topology_optimization",
  "data": {
    "mesh": "http://127.0.0.1:5200/artifacts/block_mesh.nas",
    "material": { },
    "boundaryConditions": "x = 0, ux = 0;\n y = 0, uy = 0;\n PointLoad(0.016,0.005,0,-1000)",
    "variables": ":ux, :uy",
    "elementType": ":triangle",
    "method": "ESO",
    "percentage": 50
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

### Собственные значения модели

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
  "type": "eigenfrequency",
  "data": {
    "mesh": "http://127.0.0.1:5200/artifacts/block_mesh.nas",
    "material": {
      "name": "PZT",
      "density": 7500,
      "c": [
        [
          1.38999e+011,
          7.78366e+010,
          7.42836e+010,
          0,
          0,
          0
        ],
        [
          7.78366e+010,
          1.38999e+011,
          7.42836e+010,
          0,
          0,
          0
        ],
        [
          7.42836e+010,
          7.42836e+010,
          1.15412e+011,
          0,
          0,
          0
        ],
        [
          0,
          0,
          0,
          2.5641e+010,
          0,
          0
        ],
        [
          0,
          0,
          0,
          0,
          2.5641e+010,
          0
        ],
        [
          0,
          0,
          0,
          0,
          0,
          3.0581e+010
        ]
      ],
      "g": [
        [
          1475,
          0,
          0
        ],
        [
          0,
          1475,
          0
        ],
        [
          0,
          0,
          1300
        ]
      ],
      "e": [
        [
          0,
          0,
          0,
          0,
          12.7179,
          0
        ],
        [
          0,
          0,
          0,
          12.7179,
          0,
          0
        ],
        [
          -5.20279,
          -5.20279,
          15.0804,
          0,
          0,
          0
        ]
      ]
    },
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
