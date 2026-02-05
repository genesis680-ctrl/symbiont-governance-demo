# Symbiont Governance (Demo)

> **Status:** Proof of Concept (PoC)
> **Proteção:** Ativa (Client-Side)

## 🛡️ O que é isso?
O Symbiont impede que códigos inseguros entrem no repositório.
Ele atua como um 'Guarda-Costas' no seu Git.

## 🚀 Como Testar em 1 Minuto
1. Clone o repo e entre na pasta.
2. Ative a proteção com o comando:
   `chmod +x .git/hooks/pre-commit`
3. Tente commitar um erro:
   Crie um arquivo contendo a palavra `FORCE_ERROR`.
4. Resultado: 🛑 O commit será BLOQUEADO na hora!
