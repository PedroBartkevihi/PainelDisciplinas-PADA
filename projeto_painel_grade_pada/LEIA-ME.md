# Painel de Alocação — BSI 2026.2

Arquivos do projeto, em duas camadas: os **dados** (extraídos do PDF) e o **painel** (HTML/CSS/JS que os exibe).

## Arquivos

| Arquivo | O que é |
|---|---|
| `painel-alocacao-bsi-2026-2.html` | **O painel pronto**, já com os dados embutidos. É o arquivo publicado — pode abrir direto no navegador. |
| `template.html` | Estrutura (HTML + todo o CSS) do painel, com dois marcadores `__DATA_JSON__` e `__APP_JS__` no lugar dos dados e do script. Os logos da UNIRIO e do BSI já estão embutidos como imagens `data:` — o arquivo é autossuficiente, não depende de PNGs na pasta. |
| `app.js` | Toda a lógica em JavaScript (filtros, abas, gráficos, o painel "Ao vivo", etc). |
| `allocation.json` | Os dados já tratados: disciplinas das duas grades e as 49 turmas (professor, sala, horário, vagas, a quais disciplinas cada turma atende). |
| `build_data.py` | Script Python que gera o `allocation.json` a partir dos dados extraídos do PDF original. |

## Como editar

**Mudar textos, cores, layout ou o comportamento do painel (JS)?**
Edite `template.html` (visual/CSS) ou `app.js` (comportamento) diretamente, depois remonte o HTML final (veja abaixo). Não edite `painel-alocacao-bsi-2026-2.html` diretamente se também tiver os fontes — as próximas alterações via `template.html`/`app.js` vão te obrigar a remontar de qualquer forma.

**Mudar uma turma, sala, professor ou disciplina?**
Edite `allocation.json` (ou, melhor, edite `build_data.py` e rode-o de novo — ele documenta de onde cada turma veio e evita erros de digitação, já que reexecuta as checagens de consistência). Estrutura de uma turma:
```json
{
  "id": 5,
  "professor": "Prof. Rodrigo",
  "sala": "Sala 215",
  "vagas": 40,
  "slots": [{"dia": "6ª", "inicio": "14h", "fim": "18h", "inicio_num": 14, "fim_num": 18}],
  "ofertas": [
    {"grade": "nova", "sigla": "FSI", "nome": "Fundamentos de Sistemas de Informação", "codigo": "TIN0206", "periodo": "1"},
    {"grade": "antiga", "sigla": "FSI", "nome": "Fundamentos de Sistemas de Informação", "codigo": "TIN0112", "periodo": "1"}
  ]
}
```
`ofertas` tem 1 item quando a turma existe só numa grade, e 2 quando é aula compartilhada entre a grade antiga e a nova.

## Como remontar o HTML final

Depois de editar `template.html`, `app.js` e/ou `allocation.json`, rode este script Python (precisa dos três arquivos na mesma pasta) para gerar de novo o HTML pronto:

```python
import json
with open('allocation.json', encoding='utf-8') as f:
    data = json.load(f)
data_json = json.dumps(data, ensure_ascii=False, separators=(',', ':'))
with open('template.html', encoding='utf-8') as f:
    template = f.read()
with open('app.js', encoding='utf-8') as f:
    appjs = f.read()
out = template.replace('__DATA_JSON__', data_json).replace('__APP_JS__', appjs)
with open('painel-alocacao-bsi-2026-2.html', 'w', encoding='utf-8') as f:
    f.write(out)
```

Se preferir, é só me mandar o que quer mudar aqui na conversa que eu edito e remonto para você.
