# 🔐 Secure TCP Socket: Diffie-Hellman & Cifra de César

Este projeto implementa uma arquitetura Cliente-Servidor via sockets TCP em Python, focada em comunicação segura. O tráfego de dados é protegido por criptografia de ponta a ponta, implementada do zero (código autoral), sem o uso de bibliotecas criptográficas externas.

Projeto desenvolvido para a disciplina de Redes/Segurança como requisito de avaliação (N2).

## 🚀 Funcionalidades e Requisitos Atendidos

- **Comunicação TCP/IP:** Estabelecimento de conexão confiável entre cliente (Alice) e servidor (Bob).
- **Troca de Chaves de Diffie-Hellman:** Algoritmo implementado nativamente para acordar uma chave simétrica secreta em um canal inseguro.
- **Validação de Números Primos:** Algoritmo customizado de alta performance para validar as raízes matemáticas do Diffie-Hellman.
- **Criptografia Simétrica (Cifra de César):** Algoritmo de ofuscação adaptado para a tabela ASCII, suportando caracteres especiais e espaços.
- **Parametrização via CLI:** Uso de `sys.argv` para injeção dinâmica de IP e Porta via linha de comando.

## 🧠 Arquitetura de Segurança

A comunicação segue as seguintes etapas matemáticas e lógicas:

1. **Setup Público:** Cliente e Servidor concordam previamente com um número primo `P` e uma base `G`.
2. **Geração de Chaves Privadas:** Cada lado gera um fator aleatório privado (`a` para o cliente, `b` para o servidor).
3. **Cálculo das Chaves Públicas:** - Cliente calcula: `A = (G^a) mod P`
   - Servidor calcula: `B = (G^b) mod P`
4. **Troca e Segredo Compartilhado:** Eles trocam `A` e `B` pela rede. Em seguida, calculam a chave simétrica final `S` de forma independente.
5. **Criptografia:** A chave simétrica `S` resultante atua como o fator de deslocamento (shift) dinâmico para a Cifra de César, garantindo que o texto seja cifrado antes do envio pelo socket.

## ⚙️ Como Executar

**Pré-requisitos:** Python 3.x instalado.

O projeto foi construído para ser executado via terminal. Para testar a comunicação, abra duas instâncias do terminal (podem ser na mesma máquina usando o *localhost* ou em máquinas distintas na mesma rede).

**1. Iniciando o Servidor (Bob):**

No primeiro terminal, inicie o servidor passando a porta desejada como argumento.
```bash
python3 simple_tcp_server.py 1300
```
**2. Iniciando o Cliente (Alice):**

No segundo terminal, inicie o cliente passando o IP do servidor e a mesma porta. (Use 127.0.0.1 se estiver testando localmente).
Bash

```bash
python3 simple_tcp_client.py 127.0.0.1 1300
```

**3. Interação:**

Digite a mensagem no terminal do Cliente. O Servidor receberá a mensagem cifrada, irá decifrá-la, transformá-la em maiúsculas (processamento), re-cifrá-la e devolvê-la ao Cliente.

## 🕵️‍♂️ Análise de Tráfego (Wireshark)

Conforme os requisitos da Etapa 1, a comunicação foi submetida à análise de pacotes utilizando o Wireshark.

Ao escutar a interface de rede (filtrando por tcp.port == 1300), o recurso Follow TCP Stream comprova a eficácia da implementação:
- Sem a Criptografia: O tráfego de payload flui em plaintext, permitindo que qualquer interceptador leia a mensagem.
- Com a Criptografia Ativada: O payload capturado na camada de aplicação exibe apenas caracteres ilegíveis e bytes ofuscados, mitigando ataques de interceptação simples (Eavesdropping).
