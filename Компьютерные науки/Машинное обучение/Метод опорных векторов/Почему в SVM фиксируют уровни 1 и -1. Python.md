---
jupyter:
  jupytext:
    cell_metadata_filter: -all
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.19.5
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

```python
import numpy as np
import matplotlib.pyplot as plt

# Ближайшие к границе точки имеют x1 = -2 и x1 = 2.
negative = np.array([[-3, 0], [-2, -1], [-2, 1]])
positive = np.array([[3, 0], [2, -1], [2, 1]])

def plot_scale(alpha):
    """Одна и та же граница alpha * x1 = 0 в выбранном масштабе."""
    fig, ax = plt.subplots(figsize=(9, 4), dpi=120)

    # f(x) = alpha * x1. Уровни f(x) = -1, 0, 1.
    left_margin = -1 / alpha
    right_margin = 1 / alpha
    min_functional_margin = 2 * alpha
    valid = min_functional_margin >= 1

    ax.axvspan(left_margin, right_margin, color='gray', alpha=0.12)
    ax.axvline(0, color='black', linewidth=2, label=r'$f(x)=0$')
    ax.axvline(left_margin, color='black', linestyle='--', linewidth=1.5)
    ax.axvline(right_margin, color='black', linestyle='--', linewidth=1.5)

    ax.scatter(negative[:, 0], negative[:, 1], color='royalblue', s=70)
    ax.scatter(positive[:, 0], positive[:, 1], color='tomato', s=70)

    # Вектор нормали w = (alpha, 0); его длина равна alpha.
    ax.arrow(
        0, 0, alpha, 0,
        width=0.035,
        head_width=0.22,
        head_length=0.18,
        length_includes_head=True,
        color='green',
    )

    status = r'\mathrm{valid}' if valid else r'\mathrm{invalid}'
    ax.set_title(
        rf'$\alpha={alpha:g},\ ||w||={alpha:g}$' + '\n' +
        rf'$\min(y_if_i)={min_functional_margin:g}\quad {status}$'
    )
    ax.set_xlim(-4.5, 4.5)
    ax.set_ylim(-2, 2)
    ax.set_aspect('equal')
    ax.set_xlabel(r'$x_1$')
    ax.set_ylabel(r'$x_2$')
    ax.grid(alpha=0.2)
    fig.tight_layout()
    return fig
```

Большой масштаб коэффициентов: разделяющая прямая та же, но уровни $\pm1$ расположены близко к ней.

```python
figure = plot_scale(2.0)
```

Уменьшаем масштаб — уровни $\pm1$ раздвигаются.

```python
figure = plot_scale(1.0)
```

Минимальный допустимый масштаб: уровни $\pm1$ касаются ближайших точек.

```python
figure = plot_scale(0.5)
```

Слишком маленький масштаб: ближайшие точки оказываются внутри полосы, и ограничение нарушается.

```python
figure = plot_scale(0.25)
```

Что видно на графиках:

- сплошная линия $f(x)=0$ не меняется при любом $\alpha$;
- зелёная стрелка — вектор нормали $w=(\alpha,0)$;
- пунктирные линии обозначают уровни $f(x)=-1$ и $f(x)=1$;
- при уменьшении $\lVert w\rVert=\alpha$ пунктирные границы раздвигаются;
- при $\alpha=0{,}5$ они касаются ближайших точек: минимальный функциональный отступ равен $1$;
- при $\alpha=0{,}25$ границы проходят за ближайшими точками, поэтому ограничение $y_if(x_i)\geq1$ нарушено;
- минимальная допустимая норма равна $0{,}5$, а геометрический отступ до ближайшей точки равен $1/0{,}5=2$.
