# Google Apps Script: Exportar Dados para JSON

Este script Google Apps é projetado para exportar dados de uma planilha do Google Sheets para um formato JSON. Ele seleciona os dados da aba ativa da planilha e filtra as linhas com o status `true`, convertendo esses dados em uma estrutura JSON.

## Funcionamento

1. **Abre a Planilha:**
   O script acessa a planilha ativa e seleciona a aba atualmente ativa.

2. **Obtém Dados:**
   Obtém o intervalo de dados da aba ativa e armazena os valores em um array.

3. **Extrai Cabeçalhos:**
   Obtém os cabeçalhos das colunas da primeira linha.

4. **Filtra e Converte Dados:**
   Itera sobre as linhas de dados, começando na segunda linha, e verifica se o status da linha é `true`. Se for, cria um objeto JSON com os dados da linha e o adiciona a um array de resultados.

5. **Gera JSON:**
   Converte o array de objetos JSON em uma string JSON formatada.

6. **Retorna JSON:**
   Retorna a string JSON como uma resposta HTTP com o tipo MIME `application/json`.

## Estrutura da Planilha

A planilha deve conter as seguintes colunas:

- `app_id`
- `app_name`
- `app_icon`
- `app_description`
- `app_category`
- `app_comments`
- `app_tag`
- `app_download_url`
- `app_download_count`
- `app_dev_id`
- `status` (para filtrar quais linhas devem ser exportadas)

## Código do Script

```javascript
function doGet() {
  // Abre a planilha e seleciona a aba ativa
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  
  // Obtém o intervalo de dados
  var range = sheet.getDataRange();
  var values = range.getValues();

  // Cabeçalhos das colunas
  var headers = values[0];

  // Inicializa um array para armazenar os dados JSON
  var jsonData = [];

  // Itera sobre as linhas de dados, começando na linha 1
  for (var i = 1; i < values.length; i++) {
    var row = values[i];
    var status = row[headers.indexOf('status')];
    
    // Verifica o status e adiciona ao JSON se for true
    if (status === true) {
      var jsonObject = {
        app_id: row[headers.indexOf('app_id')],
        app_name: row[headers.indexOf('app_name')],
        app_icon: row[headers.indexOf('app_icon')],
        app_description: row[headers.indexOf('app_description')],
        app_category: row[headers.indexOf('app_category')],
        app_comments: row[headers.indexOf('app_comments')],
        app_tag: row[headers.indexOf('app_tag')],
        app_download_url: row[headers.indexOf('app_download_url')],
        app_download_count: row[headers.indexOf('app_download_count')],
        app_dev_id: row[headers.indexOf('app_dev_id')]
      };
      
      jsonData.push(jsonObject);
    }
  }
  
  // Converte o array JSON em uma string
  var jsonString = JSON.stringify(jsonData, null, 2);
  
  // Retorna a string JSON como resposta HTTP
  return ContentService.createTextOutput(jsonString).setMimeType(ContentService.MimeType.JSON);
}
