# 🏛️ Symbiont Governance Protocol (PoC)

**Status:** Technical Proof of Concept (v1.0-demo)  
**Architecture:** Client-Side Deterministic Governance  
**License:** Proprietary / Demo Access Only

---

## 1. Visão Geral
O **Symbiont** é uma infraestrutura de governança como código (Governance-as-Code) projetada para ambientes de alta conformidade. Ele atua como um "firewall de commits", impedindo que código inseguro, credenciais vazadas ou violações de arquitetura entrem no controle de versão.

## 2. O Problema de Mercado
* **Detecção Tardia:** Ferramentas de CI/CD (SaaS) só detectam falhas *depois* que o código já saiu da máquina do desenvolvedor.
* **Risco de Vazamento:** Uma vez que uma chave privada é commitada, ela permanece no histórico do Git mesmo se deletada depois.
* **Custo de Retrabalho:** Corrigir bugs na esteira de CI custa 10x mais do que no ambiente local.

## 3. A Solução Symbiont
O Symbiont move a segurança para a esquerda (**Shift-Left Security Extreme**). Ele implementa uma engine de validação local que intercepta a ação de commit.

* **Bloqueio Hard-Fail:** O commit é recusado fisicamente se houver violações críticas.
* **Auditoria Determinística:** Regras baseadas em AST (Abstract Syntax Tree) e Regex de alta performance.
* **Zero Latência:** Roda localmente, sem dependência de internet ou containers Docker pesados.

## 4. Arquitetura Lógica

```mermaid
graph LR
    A[Developer] -->|git commit| B(Git Hook Interceptor)
    B --> C{Symbiont Engine}
    C -->|Leitura| D[Rule Manifest]
    C -->|Scan| E[Staged Files]
    C --> F{Veredito}
    F -- BLOCK --> G[❌ Abort Commit]
    F -- PASS --> H[✅ Push to Cloud]
mkdir engine

cat > engine/validator_demo.py << 'EOF'
import sys
import os

# CONFIGURAÇÃO DEMO
# A "senha" que ativa o bloqueio para testar
VIOLATION_TRIGGER = "FORCE_ERROR"

def scan_files():
    print("🛡️  SYMBIONT ENGINE (DEMO) - Iniciando Scan...")
    
    violation_found = False

    # Varre os arquivos da pasta atual
    for root, dirs, files in os.walk("."):
        for file in files:
            # Só olha arquivos Python e ignora o próprio validator
            if file.endswith(".py") and "validator" not in file:
                try:
                    with open(os.path.join(root, file), 'r') as f:
                        content = f.read()
                        
                        # Se achar a palavra proibida, avisa
                        if VIOLATION_TRIGGER in content:
                            print(f"❌ [CRITICAL] Violação de Segurança em: {file}")
                            print(f"   -> Motivo: Padrão restrito '{VIOLATION_TRIGGER}' encontrado.")
                            violation_found = True
                            
                except Exception:
                    pass

    if violation_found:
        print("\n🔴 FATAL: Política de Governança violada. Commit BLOQUEADO.")
        sys.exit(1)
    
    print("✅ SUCESSO: Código aprovado pela governança básica.")
    sys.exit(0)

if __name__ == "__main__":
    scan_files()
