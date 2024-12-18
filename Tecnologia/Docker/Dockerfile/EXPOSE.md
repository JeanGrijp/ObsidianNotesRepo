---
tags:
  - Tecnologia
  - Docker
---
A instrução **`EXPOSE`** no [[Dockerfile]] é usada para **informar quais portas o container estará escutando** para conexões de rede. Em resumo, é uma **declaração de intenção** que ajuda outros desenvolvedores e ferramentas (como o Docker [[Compose]]) a entenderem quais portas o container utiliza, mas **não configura diretamente o acesso às portas fora do [[container]]**.

### Como o `EXPOSE` funciona:

1. Ele **documenta** as portas que o container vai usar.
2. **Não abre portas automaticamente no host**. Para que as portas fiquem acessíveis, você deve mapeá-las ao rodar o container usando a flag `-p` ou `-P`.

### Formato básico:

```Dockerfile
EXPOSE <porta> [protocolo]
```

- `<porta>`: A porta que o container estará escutando (obrigatório).
- `[protocolo]`: Pode ser `tcp` ou `udp`. O padrão é `tcp`.

### Exemplos:

#### 1. Declarar a porta de um servidor web

```Dockerfile
FROM nginx:latest
EXPOSE 80
```

Aqui, o `EXPOSE 80` indica que o container escutará a porta 80 para conexões (como um servidor web). Para acessá-lo, você precisa mapear a porta ao rodar o container:

```bash
docker run -p 8080:80 meu-container
```

Agora, o serviço estará disponível no host na porta `8080`, redirecionando para a porta `80` do container.

#### 2. Especificar o protocolo

```Dockerfile
EXPOSE 53/udp
EXPOSE 80/tcp
```

Neste exemplo:

- A porta 53 será usada para comunicação via protocolo UDP.
- A porta 80 será usada para comunicação via protocolo TCP.

#### 3. Com várias portas

```Dockerfile
EXPOSE 3000 3001 3002
```

Este comando expõe as portas 3000, 3001 e 3002.

### `EXPOSE` e `-P` (exposição automática)

Se você usar o comando `docker run -P`, o Docker automaticamente mapeia todas as portas expostas no Dockerfile para portas aleatórias no host.

Exemplo:

```bash
docker run -P meu-container
```

Se o Dockerfile tiver:

```Dockerfile
EXPOSE 80
```

O Docker cria algo como:

```
Host: 0.0.0.0:49153 -> Container: 80
```

### Quando usar o `EXPOSE`:

1. **Documentação**:
    - Indica quais portas sua aplicação utiliza, facilitando a colaboração e entendimento do projeto.
2. **Trabalhando com Docker Compose**:
    - O `EXPOSE` é útil para integrar serviços, pois o Compose entende quais portas precisam ser conectadas.

### Cuidados:

1. **Não abre portas no host automaticamente**:
    - Você ainda precisa usar a flag `-p` ou `-P` para expor portas fora do container.
2. **Boa prática, mas não obrigatória**:
    - Mesmo sem o `EXPOSE`, você pode mapear portas com `docker run -p`.