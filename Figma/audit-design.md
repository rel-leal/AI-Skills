---
name: design-audit
description: Audita um design selecionado verificando consistência de cores, tipografia, contraste WCAG, layers ocultos/vazios e uso de estilos e variáveis do design system.
---

# Design Audit

Executa uma auditoria completa do design selecionado e gera um relatório estruturado com problemas encontrados e recomendações.

## Etapas

### 1. Inventário de Cores

- Percorra todos os nodes dentro da seleção e colete todas as cores de fill e stroke.
- Agrupe por valor HEX e conte quantas vezes cada cor aparece.
- Identifique cores que não estão vinculadas a um estilo local ou variável (hardcoded).
- Detecte cores quase-duplicadas (distância euclidiana RGB < 15 no espaço 0–255) e sugira unificação.

### 2. Inventário de Tipografia

- Colete todas as combinações de font family, weight, size e line-height usadas em nodes de texto.
- Liste cada combinação com a contagem de uso.
- Identifique textos que não usam um text style local.
- Sinalize inconsistências como tamanhos de fonte muito próximos (ex: 14px e 15px usados no mesmo contexto).

### 3. Verificação de Contraste (WCAG AA)

- Para cada node de texto, calcule o contraste entre a cor do texto e a cor de fundo mais provável (fill do parent direto ou do frame mais próximo).
- Use a fórmula de luminância relativa WCAG: L = 0.2126 × R_linear + 0.7152 × G_linear + 0.0722 × B_linear.
- Razão de contraste: (L_mais_claro + 0.05) / (L_mais_escuro + 0.05).
- Sinalize textos que não atingem 4.5:1 para texto normal ou 3:1 para texto grande (≥ 24px).

### 4. Organização de Layers

- Conte e liste layers ocultos (visible === false).
- Identifique frames vazios (sem filhos).
- Sinalize grupos ou frames com apenas 1 filho (nesting desnecessário).
- Conte a profundidade máxima da árvore de layers e sinalize se exceder 10 níveis.

### 5. Uso do Design System

- Verifique quantos nodes usam estilos locais vs. valores hardcoded (fills, strokes, text styles, effects).
- Calcule a porcentagem de adoção do design system.
- Verifique se há variáveis vinculadas e quantas propriedades usam variáveis vs. valores fixos.

## Formato do Relatório

Apresente os resultados como um relatório estruturado com seções e emojis de status:

- ✅ para itens sem problemas
- ⚠️ para avisos (inconsistências menores)
- ❌ para problemas críticos (falhas de contraste, muitas cores hardcoded)

Inclua no final um resumo com:
- Score geral de consistência (porcentagem de adoção de estilos/variáveis)
- Top 3 ações prioritárias para melhorar o design

## Notas Técnicas

- Lembre-se que node.fills pode ser figma.mixed — sempre verifique com === figma.mixed antes de acessar .length.
- Use findAllWithCriteria para buscas por tipo de node.
- Valores RGB no Figma são 0–1, converta para 0–255 quando necessário.
