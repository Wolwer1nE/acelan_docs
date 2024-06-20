## Регулярные стержневые сетки

```
regular_bars(nbx, nby, nbz, l)
apply_condition(" ")
solve

# таблица и график

surface(:ux, fix: :x, at: 0)
line(:ux, fix: [:x, :y], at: [0,2])
```