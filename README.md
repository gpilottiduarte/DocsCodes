# Documentation Readability Analyzer

## Overview

O Documentation Readability Analyzer é um workflow do GitHub Actions que avalia automaticamente a legibilidade de arquivos de documentação Markdown em um repositório, gerando métricas e estatísticas abrangentes para avaliação da qualidade da documentação.

## Arquitetura Técnica

### Componentes Principais

O sistema é composto por três componentes primários:

- Configuração de Workflow do GitHub Actions
- Motor de Análise baseado em Python
- Sistema de Geração de Relatórios Automatizado

## Dependências

### Dependências Core

- Python 3.10
- textstat >= 0.7.3
- pandas >= 2.0.0

## Linting de Documentação

### Vale Linter

- **Caminho de Configuração**: `.github/styles`
- **Nível Mínimo de Alerta**: sugestão
- **Implementação de Estilo**: Estilo padrão Vale

## Regras Personalizadas do Vale

### Estrutura de Regras

```yaml
StylesPath = styles
MinAlertLevel = suggestion
[*.{md,mdx}]
BasedOnStyles = senhasegura
```

### Regras Personalizadas

#### Validação de Padrão de Título

```yaml
extends: regex
message: "Títulos devem seguir o padrão: number_language_type_name"
level: error
scope: text
pattern: '\d{2}_(PT|EN)_(REF|EXP|HOW)_[A-Za-z]+'
```

#### Regras Específicas de Produto

```yaml
extends: substitution
message: "Use o nome do módulo correto: '%s'"
level: error
ignorecase: false
swap:
  "devops manager": "DevOps Secret Manager"
  "password manager": "Password Safe"
```

## Níveis de Validação

- **erro**: Problemas críticos de documentação
- **aviso**: Recomendações de estilo
- **sugestão**: Melhorias opcionais

## Processo de Análise

### Classe ReadabilityAnalyzer

#### Descoberta de Arquivos

- Identifica recursivamente arquivos Markdown
- Exclui arquivos padrão (CHANGELOG.md, LICENSE.md, etc.)

#### Análise de Conteúdo

Calcula métricas de legibilidade por arquivo:
- Flesch Reading Ease
- Flesch-Kincaid Grade Level
- Gunning Fog Index
- SMOG Index
- Coleman-Liau Index
- Automated Readability Index
- Dale-Chall Score

## Formato de Saída

### Estrutura do Relatório JSON

```json
{
    "timestamp": "Timestamp ISO-8601",
    "summary": {
        "total_files": "Número inteiro",
        "configuration": {
            "reading_speed_wpm": "Número decimal"
        }
    },
    "results": [
        {
            "filepath": "Caminho do arquivo",
            "metrics": {
                "flesch_reading_ease": "Número decimal"
            },
            "statistics": {
                "word_count": "Número decimal"
            }
        }
    ]
}
```

## Configuração

### Variáveis de Ambiente

- `READING_SPEED_WPM`: Padrão 200 (palavras por minuto)

## Localização de Saída

- Relatório JSON detalhado: `readability_report/detailed_report.json`
- Artefatos do GitHub Actions: Carregado como `readability-report`
