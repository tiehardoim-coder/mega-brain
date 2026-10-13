# Revisão da validação - 13/10/2026

Snapshot examinado: `389b20f88dcf0d0955b1ea325dbf6c51664bb12c`.

## Problema e alteração

O script `npm run validate` chamava `node bin/mega-brain.js validate`. Sem `.env`, esse caminho iniciava o assistente de configuração e tentava instalar dependências. O roteador também não possui um caso `validate`.

O script agora executa os validadores que já pertencem ao projeto:

```bash
npm run validate:json && npm run validate:layers
```

Com isso, a validação pode ser usada em uma cópia sem credenciais e preserva o código de saída dos validadores. O comando não aciona o setup da aplicação. As regras de classificação do pacote não foram flexibilizadas.

## Resultados observados

Ambiente: Node.js 24.19.0 e Python 3.12.14. Dependências Node instaladas por `npm ci --ignore-scripts --no-audit --no-fund`.

| Verificação | Resultado |
|---|---|
| Integridade JSON | 14 de 14 arquivos válidos |
| `npm run validate` após a correção | Executa os dois validadores e retorna 1 devido às pendências reais de classificação |
| Classificação do pacote | 957 arquivos examinados; 590 classificados como `REVIEW` na execução registrada |
| Testes Python declarados no package.json | Diretório `tests/python/` ausente no snapshot |
| Operações que usam IA externa | Não executadas; dependem de configuração própria |

Os 590 itens são pendências do classificador interno, não uma conclusão de exposição de dados. Uma próxima revisão deve conferir o mapeamento de camadas e a composição do pacote antes de alterar as regras. A relação pode variar se a árvore de trabalho mudar.

## Pendências

1. Reconciliar o classificador com os caminhos efetivamente distribuídos.
2. Restaurar ou fornecer a suíte `tests/python/` referenciada pelos scripts e workflows.
3. Configurar o ambiente de execução e validar ingestão e consultas com dados de teste antes do uso operacional.
