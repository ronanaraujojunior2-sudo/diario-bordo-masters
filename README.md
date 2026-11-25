# Diário de Bordo – Masters Engenharia (novo sistema)

Sistema novo e independente. Não usa o código, o Firebase nem a publicação do aplicativo anterior.

## Situação atual: versão de apresentação

- Roda em **modo demonstração**: dados fictícios gravados só no navegador (localStorage), com faixa "Ambiente de demonstração" sempre visível.
- Perfis de demonstração (senha `demo`): `MASTERS` (administrador), `RONAN`, `ECCLESTON`, `VALDINEI`.
- "Restaurar demo", no menu, volta à base fictícia original.

## Estrutura

| Caminho | Conteúdo |
|---|---|
| `index.html` | Layout, identidade visual e estilos (sem etapa de build) |
| `app.js` | Telas, regras de negócio e camada de dados `DB` (hoje: demonstração) |
| `assets/` | Logotipo original da Masters e ícones |
| `supabase/migrations/001_init.sql` | Esquema PostgreSQL com RLS, auditoria, última km e funções de uso |
| `supabase/teste_regras.sql` | Teste das regras (executado em Postgres local: todas passaram) |

Bibliotecas por CDN: Chart.js 4.4.1 (gráficos) e SheetJS 0.18.5 (Excel).

## Regras implementadas

- MASTERS é o único administrador e nunca assume, reserva ou é responsável por veículo (tela e banco).
- Um veículo só pode estar com uma pessoa por vez; cada pessoa só com um veículo.
- Funcionário só registra viagem/abastecimento com o veículo assumido; só libera o próprio veículo.
- Liberação administrativa exige confirmação e justificativa, e fica na auditoria.
- Última km = maior km de chegada entre as viagens de todos os usuários do veículo; nunca retrocede. Viagem nova não pode começar abaixo dela. Lacunas e retrocessos aparecem em "Precisa de atenção" sem alterar os registros originais.
- Idempotência: cada formulário tem um ID único (clique repetido, recarga ou reenvio não duplicam). Abastecimento com mesmo veículo, data, hora e valor é recusado.
- Importação no **mesmo layout do sistema anterior**: CSV do "Exportar CSV" (`;`, seções de viagens e abastecimentos) e planilha "Controle de Utilização Veículo". Prévia com novos, existentes, duplicados, conflitantes e rejeitados; nada é gravado sem confirmação.
- Exportação: Excel no layout "Controle de Utilização Veículo", CSV reimportável no layout antigo e PDF (impressão do navegador).

## Pendências (Prioridade 2 — consolidação)

1. Criar o projeto Supabase exclusivo (conta gratuita já está no limite de 2 projetos: exige plano Pro ou pausar um projeto).
2. Aplicar `001_init.sql`, criar usuários no Supabase Auth (convite por e-mail) e trocar a camada `DB` de demonstração pela do Supabase.
3. Testar RLS com usuários reais (as regras de negócio já foram testadas; as políticas RLS ainda não).
4. Edição de registros próprios e correção administrativa com auditoria.
5. Receber a planilha oficial e conferir o mapeamento de colunas.
6. Importação histórica do sistema anterior (depende de exportação/backup do Firebase).
7. Homologação e publicação definitiva; a troca do sistema antigo só com aprovação.
