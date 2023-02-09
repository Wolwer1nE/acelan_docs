```
rc = 0.0075
hc = 0.005
a = 0.005
b = 0.01
d = 0.22
dc = 0.03
rs = 0.0025
rn = 0.001
rc2 = 2 * hc

x1 = 2 * hc
x2 = x1 + hc
x3 = x2 + a
x4 = x3 + b
x5 = x4 + d
x6 = x5 + dc

bodies = [] # создание тела

points1 = [] # создание масива точек для контура

# добавление точек в контур points1
points1 << point(0, 0)
points1 << point(0, rc2)
points1 << point(x1, rc2)
points1 << point(x1, 0)
bodies << countor(points1) # создание контура и добавление его в тело

points2 = []
points2 << point(x1, 0)
points2 << point(x1, rc)
points2 << point(x2, rc)
points2 << point(x2, 0)
bodies << countor(points2)

points3 = []
points3 << point(x2, 0)
points3 << point(x2, rc)
points3 << point(x3, rc)
points3 << point(x4, rs)
points3 << point(x5, rs)
points3 << point(x5, 0)
bodies << countor(points3)

points4 = []
points4 << point(x5, 0)
points4 << point(x5, rs)
points4 << point(x6, rn)
points4 << point(x6, 0)
bodies << countor(points4)

mesh1 = mesh(bodies) # построение сетки тела
material = load_material("pzt") # загрузка материала pzt
boundary_conditions = "x = 0, ux = 0; y = 0, uy = 0; z = 0, uz = 0;"
# задание граничных условиий
A = build_matrix(mesh1, material, boundary_conditions)
n = 2
eigen_values(A, n, 10)  # нахождение собственных значений
```