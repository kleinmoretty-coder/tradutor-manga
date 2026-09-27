# Manga & Webcomic Translator

Extensão de navegador que traduz mangás e webcomics direto na página. O processamento roda localmente — a única dependência externa é a API de OCR.

[![Status](https://img.shields.io/badge/status-em_desenvolvimento-orange?style=flat-square)](#roadmap)
[![Manifest](https://img.shields.io/badge/manifest-v3-blue?style=flat-square)](https://developer.chrome.com/docs/extensions/mv3/)
[![JavaScript](https://img.shields.io/badge/vanilla-js-f7df1e?style=flat-square&logo=javascript&logoColor=black)](#stack)
[![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

<br>

<img width="280" align="left" src="https://github.com/user-attachments/assets/07cd416d-5a2d-4621-80d4-015702da5284" alt="Configuração Inicial" />
<img width="280" src="https://github.com/user-attachments/assets/0428ac2c-f52f-4f82-b249-5ac7af444ee4" alt="Sessão Ativa" />

<br clear="left">

<sub>À esquerda: detecção automática de idioma com confirmação do usuário. À direita: painel de controle da sessão.</sub>

<br>

## Recursos

**Detecção automática de idioma.** Identifica o idioma original do capítulo e sugere a configuração para o usuário.

**Download resiliente.** Captura imagens mesmo em sites que bloqueiam requisições externas, com fallback via Canvas.

**OCR em lote.** Agrupa imagens respeitando limites de altura e peso da API, evitando estouro de cota.

**Detecção de troca de capítulo.** Funciona em SPAs que não recarregam a página, via interceptação de `history.pushState`.

**UI injetada sob demanda.** Nenhum CSS ou HTML é carregado na página hospedeira até ser necessário.

**Cache persistente.** Capítulos processados ficam em IndexedDB para reuso.

<br>

## Instalação

**Pré-requisitos:** qualquer navegador baseado em Chromium (Chrome, Edge, Brave, Opera, Vivaldi) e uma chave de API do [Azure Computer Vision](https://azure.microsoft.com/pt-br/products/ai-services/ai-vision).

```bash
# 1. Clone o repositório
git clone https://github.com/seu-usuario/manga-translator.git
cd manga-translator

# 2. Configure sua chave de API
cp config.example.js config.js
# Edite config.js com seu apiKey e endpoint do Azure
```

**3. Carregue no navegador:**

1. Acesse `chrome://extensions/` (ou o equivalente do seu navegador)
2. Ative o **Modo do desenvolvedor**
3. Clique em **Carregar sem compactação** e selecione a pasta do projeto

<br>

## Uso

1. Abra um capítulo em um site suportado (ex: [MangaDex](https://mangadex.org))
2. Clique no ícone da extensão
3. Confirme o idioma de origem detectado
4. Escolha o idioma de destino e clique em **Iniciar tradução**

A extensão passa a monitorar a página. Ao trocar de capítulo, ela detecta a mudança e pergunta se deseja continuar.

<br>

## Como funciona

O projeto é dividido em três camadas:

**Content script.** Roda na página do mangá. Detecta troca de capítulo, mapeia imagens, gerencia scroll infinito e injeta a UI.

**Service worker.** Centraliza o acesso ao storage, valida mensagens vindas do content script e coordena o processamento em background.

**Módulos dinâmicos.** Carregados sob demanda via `web_accessible_resources`. Incluem `AvisoManager` (UI), `UrlMonitor` (detecção de mudança), `filtro` (processamento de imagens) e utilitários.

<br>

## Stack

| Camada | Tecnologia |
|---|---|
| Runtime | Chrome Extension Manifest V3 |
| Linguagem | JavaScript (ES2022+), sem transpilação |
| Processamento de imagem | Canvas API, OffscreenCanvas, ImageBitmap |
| Cache | IndexedDB, `chrome.storage.local` |
| OCR | Azure Computer Vision API *(temporário, ver roadmap)* |

Sem framework, sem bundler, sem `node_modules`. A única dependência externa é a API de OCR — usada por enquanto apenas para validar o pipeline. O objetivo de longo prazo é substituí-la por uma solução local, mantendo a extensão funcional offline.

<br>

## Roadmap

- [x] Detecção automática de idioma
- [x] Sistema de UI injetável (`AvisoManager`)
- [x] Camada de storage com auto-cura
- [x] Import dinâmico de módulos
- [x] Mapeamento de imagens e detecção de troca de capítulo
- [ ] Pipeline de OCR end-to-end
- [ ] Overlay de tradução sobre a página
- [ ] Substituir Azure por motor de OCR local (offline)
- [ ] Refatoração completa para OOP + princípios SOLID
- [ ] Suporte a sites além de MangaDex

<br>

## Contribuindo

Issues e pull requests são bem-vindos. Para mudanças grandes, abra uma issue antes para alinharmos a direção.

<br>

## Licença

Este projeto está licenciado sob a [MIT](LICENSE).
