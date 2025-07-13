# Desafio Técnico Integração de APIs no n8n

Este projeto resolve o desafio técnico proposto pela Altaa, integrando diferentes endpoints da BrasilAPI no n8n para automatizar a consulta de informações de empresas e previsão do tempo para a cidade da empresa consultada via CNPJ.

## Objetivo

Automatizar, via n8n, o seguinte fluxo:

- Receber um CNPJ como entrada.
- Buscar os dados do CNPJ na BrasilAPI.
- Extrair a cidade da empresa.
- Buscar a previsão do tempo para essa cidade.
- Retornar um JSON com CNPJ, razão social, cidade e previsão do tempo.
- Tratar erros de API, CNPJ inválido, cidade não encontrada, etc.

## Como executar/testar

### Pré-requisitos

- Instância do n8n rodando (local ou cloud).
- Importar o arquivo `Busca_previs_o_do_tempo_por_cidade_de_um_CNPJ.json` no n8n.
- Não é necessário configurar credenciais, pois as APIs da BrasilAPI são públicas.

### Passo a passo

1. Importe o workflow no n8n.
2. Clique em **Execute Workflow**.
3. Insira o CNPJ desejado no node de entrada manual (exemplo: `40.496.921/0001-01`).
4. O fluxo irá retornar um JSON com as informações da empresa e a previsão do tempo para os próximos 3 dias.

### Exemplo de entrada

```json
{
  "cnpj": "40.496.921/0001-01"
}
```

### Exemplo de saída

```json
{
  "cnpj": "40496921000101",
  "razao_social": "MAGAZINE LUIZA S.A.",
  "cidade": "Franca",
  "previsao_do_tempo": [
    {
      "data": "2025-07-10",
      "condicao": "Sol com algumas nuvens",
      "min": 17,
      "max": 26
    },
    ...
  ]
}
```

## Descrição do Workflow

O fluxo é composto pelos seguintes nodes principais:

- **Manual Trigger:** Inicia o fluxo manualmente e recebe o CNPJ.
- **Edit Fields:** Remove caracteres especiais do CNPJ para padronizar a consulta.
- **Busca CNPJ:** Consulta a BrasilAPI para obter os dados da empresa.
- **Busca ID Cidade:** Usa o nome do município retornado para buscar o ID da cidade na BrasilAPI.
- **Busca previsão do tempo:** Com o ID da cidade, busca a previsão do tempo para os próximos 3 dias.
- **Resultado final:** Monta o JSON de saída com todas as informações.
- **Tratamento de erros:** Diversos nodes de erro capturam e retornam mensagens amigáveis caso alguma etapa falhe (CNPJ inválido, cidade não encontrada, API fora do ar, etc).

## Tratamento de erros

- **CNPJ inválido:** O fluxo para e retorna uma mensagem de erro clara.
- **Cidade não encontrada:** O fluxo retorna o CNPJ e razão social, mas sem previsão do tempo, e informa o erro.
- **API fora do ar ou resposta inesperada:** Mensagens de erro específicas são retornadas para facilitar o diagnóstico.

## Decisões técnicas e problemas encontrados

- **Padronização do CNPJ:** Utilizei um node Set para remover pontos, barras e traços, garantindo compatibilidade com a API.
- **Busca de cidade:** A API de previsão do tempo exige o ID da cidade, então foi necessário um passo intermediário para buscar esse ID a partir do nome do município.
- **Tratamento de erros:** Usei nodes de Stop and Error para capturar e exibir mensagens detalhadas de falha em cada etapa.
- **Limitações:** A API de previsão do tempo pode não ter dados para todas as cidades retornadas pelo CNPJ. O fluxo lida com isso retornando o máximo de informações possível.

## Possíveis melhorias e diferenciais

- **Logs detalhados:** Poderia ser adicionado um node para registrar logs em Google Sheets ou outro sistema.
- **Consulta em lote:** O fluxo pode ser adaptado para receber uma lista de CNPJs e processar todos em sequência.
- **Modularização:** Sub-workflows podem ser criados para tornar o fluxo mais reutilizável.
- **Sugestões para produção:** Adicionar autenticação, monitoramento de falhas, e alertas em caso de erro recorrente.

## Prints do fluxo
![image.png](attachment:0ab7f473-f1bf-49bc-861a-ec9b630364d3:image.png)
