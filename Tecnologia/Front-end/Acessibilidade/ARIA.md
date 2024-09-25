---
tags:
  - Tecnologia
  - front-end
---
As propriedades ARIA (Accessible Rich Internet Applications) nas tags HTML, foram criadas para dar mais assertividade às funcionalidades dos elementos no front-end. Por exemplo, um elemento `button` não tem nenhum recurso HTML nativo para expor um estado "pressionado". ARIA permite que desenvolvedores estendam a semântica do elemento especificando o atributo `aria-pressed`, permitindo uma UI auditiva que corresponderá à apresentação visual do controle.

```HTML
<button aria-pressed=true>...</button>
```

Há também situações em que certos `aria-*`atributos são permitidos para uso em elementos com `role`s específicos, enquanto o atributo nativo equivalente não é válido atualmente no próprio HTML. Por exemplo, HTML não tem um conceito direto de um hyperlink desabilitado ( `a href`). Construções como `<a href="..." disabled> ... </a>`não são válidas e não serão transmitidas para tecnologias assistivas.

ARIA diverge do HTML nesse aspecto e permite que um atributo `aria-disabled` seja especificado em um elemento com um explícito `role=link`. Se alguém especificasse um `aria-disabled=true`em uma tag HTML, os usuários não tratariam funcionalmente o hiperlink de forma diferente (ele ainda seria clicável/operável), no entanto, ele seria exposto a tecnologias assistivas como estando no estado desabilitado.

```HTML
<a role=link aria-disabled=true>...</a>
```

Da mesma forma, enquanto elementos HTML nativos `option` que são descendentes de a `select` só podem ser definidos como sendo `selected`, elementos com uma função explícita `option` podem não apenas permitir o equivalente `aria-selected`, mas também o atributo `aria-checked`, suportando widgets/construções que vão além das capacidades de um `select` nativo.