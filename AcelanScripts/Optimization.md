```
h = 1
a = 3

bodies = [] # создание тела

points1 = [] # создание масива точек для контура

# добавление точек в контур points1
points1 << point(0, 0)
points1 << point(0, h)
points1 << point(a, 0)
points1 << point(a, h)
bodies << countor(points1) # создание контура и добавление его в тело

mesh1 = mesh(bodies) # построение сетки тела
material = load_material("steel") # загрузка материала steel
boundary_conditions = "x = 0, ux = 0; y = 0, uy = 0"
# задание граничных условиий
model1 = model(mesh1, material, boundary_conditions)

n = 1000 # максимальное количество итераций
d = 3 # % элементов для удаления за 1 шаг
minimization = "total energy" # функция минимизации 
limitations = "material = 50%" # функция ограничения

model2 = eso(model1, minimization, limitations, d, n)  # оптимизация топологии модели
```