# QR-09 — Account Linking Identity Preservation

## 1. Risk identification
QR-09

## 2. Risk statement
A vinculação entre Google e E-mail/Senha pode criar ou utilizar uma identidade diferente, fazendo o perfil e os dados de viagem parecerem perdidos.

## 3. Objective
Validar o caminho positivo e a preservação da identidade ao vincular uma conta Google a um perfil existente baseado em E-mail e Senha, garantindo a permanência dos dados e a disponibilidade de ambos os provedores para autenticação subsequente.

## 4. Scope
- Conta E-mail/Senha existente.
- Perfil e mapa existentes.
- Vinculação do provedor Google.
- Permanência dos dois provedores configurados.
- Logout e reautenticação pelo Google.
- Recuperação da mesma identidade, perfil e dados (mapa, indicadores, badges).
- Confirmação de que o login por E-mail/Senha continua possível.

## 5. Out of scope
- Popup cancelado pelo usuário.
- Vinculação com e-mail Google diferente do principal.
- Erro `credential-already-in-use`.
- Erro `account-exists-with-different-credential`.
- Erro `requires-recent-login`.
- Falha de reautenticação e cenários de rollback.
- Interrupção da operação no meio do fluxo.
- Associação a identidade errada em caminhos negativos.
(Estes cenários de borda e tratamento de falhas pertencem ao QR-10).

## 6. Environment
- AtlasBadge Production
- Aplicação publicada na Vercel
- Firebase Authentication Production
- Cloud Firestore Production
- Navegador desktop (Microsoft Edge)
- Execução manual pelo Test Lead (2026-07-25)

## 7. Preconditions
- Conta criada com E-mail e Senha.
- E-mail verificado.
- Perfil configurado e existente.
- Mapa preenchido.
- Três locais visitados registrados.
- Badges existentes já desbloqueados.
- Google ainda não vinculado à conta.

## 8. Test data
- Identidade de teste em Production baseada em E-mail e Senha.
- Indicadores: 3/195 Países visitados, 0/52 Territórios, 3/251 Locais visitados.

## 9. Execution steps
| Step | Action |
|---|---|
| 1 | Registrar o estado inicial do perfil e do mapa. |
| 2 | Abrir Editar Perfil. |
| 3 | Confirmar Google como não conectado. |
| 4 | Confirmar E-mail e Senha como configurada. |
| 5 | Clicar em Conectar Google. |
| 6 | Concluir a autenticação Google. |
| 7 | Confirmar mensagem de sucesso. |
| 8 | Confirmar os dois métodos configurados. |
| 9 | Sair (Logout). |
| 10 | Entrar pelo Google. |
| 11 | Confirmar o mesmo perfil carregado. |
| 12 | Confirmar o mesmo mapa carregado. |
| 13 | Confirmar os mesmos indicadores. |
| 14 | Confirmar os mesmos badges. |
| 15 | Confirmar que E-mail e Senha continua configurado. |

## 10. Expected results
- Vinculação concluída com sucesso ao utilizador existente (mesmo UID).
- Mesma identidade visualmente confirmada.
- Mesmos dados carregados (mapa, locais, badges).
- Nenhum perfil novo ou paralelo criado.
- Nenhum fluxo de onboarding reapresentado.
- Ambos os métodos de acesso (Google e E-mail/Senha) funcionais.

## 11. Actual results
- Passed.
- O mesmo perfil foi recuperado perfeitamente.
- O mapa foi integralmente preservado.
- Os indicadores (3/195, 0/52, 3/251) foram preservados.
- Os badges foram preservados.
- Google apareceu como "Conectado".
- E-mail e Senha apareceu como "Configurada".
- Nenhum indício de duplicação de perfil ou UID foi observado. A funcionalidade está operando perfeitamente.

## 12. Technical implementation review
A vinculação é realizada corretamente em `src/components/auth/AuthMethods.tsx` utilizando o usuário ativo do contexto de autenticação:

- **Handler de Linking:** `handleConnectGoogle`
- **Uso do currentUser:** A operação utiliza explicitamente a referência `user` proveniente de `useAuth()` (o utilizador autenticado).
- **Operação Link:** Ocorre através de `linkWithPopup(user, provider)`.
- **Provider IDs:** O sistema consolida os métodos associando o `GoogleAuthProvider` e preservando o login por `password`.
- **Validação de Identidade:** Há uma checagem rigorosa de integridade `if (result.user.uid !== originalUid)` para garantir que não houve mudança de identidade na transação (impedindo fluxos paralelos não intencionais).
- **Armazenamento:** Nenhuma duplicação de documentos é orquestrada. O Firebase Auth atualiza o objeto do utilizador, e o Cloud Firestore continua buscando dados a partir de caminhos baseados unicamente no UID (`users/{uid}` e `users/{uid}/places/{placeId}`).

## 13. Visual evidence

| Evidence | Description | Verification |
|---|---|---|
| [QR-09-01](ab-ev-005-qr-09-01-existing-profile-before-linking.png) | Existing profile and travel data before linking | Baseline preserved |
| [QR-09-02](ab-ev-005-qr-09-02-access-methods-before-linking.png) | Google disconnected; password configured | Initial provider state |
| [QR-09-03](ab-ev-005-qr-09-03-google-linked-successfully.png) | Google connected successfully; password retained | Both providers linked |
| [QR-09-04](ab-ev-005-qr-09-04-same-profile-after-google-login.png) | Same profile and map after Google login | Identity and data preserved |

## 14. Data preservation verification
Conforme inspecionado no Technical Review, os caminhos Firestore permanecem ancorados à propriedade `uid` invariável do token de sessão. A vinculação de provedor não desencadeia a geração de um novo UID, protegendo nativamente a integridade relacional dos dados.

## 15. Result
Passed — Mitigated and retained in regression coverage. A vinculação de conta não compromete a identidade no ambiente Production V1.0.

## 16. Residual risk
- Regressões futuras no pacote Firebase Authentication.
- Alteração inadvertida do fluxo de vinculação ou do método `linkWithPopup`.
- Mudança na modelagem de provedor e mapeamento de UIDs.
- Modificação no carregamento centralizado de dados (`AuthContext` ou esquemas baseados em UID).
**Tratamento residual:** Manter o cenário positivo do QR-09 nas baterias de regressão manual de autenticação e disparar revalidações em qualquer atualização estrutural da biblioteca Auth.

## 17. Test Lead approval
Aprovado por Test Lead em 25 de julho de 2026. A funcionalidade atende plenamente ao comportamento exigido e as evidências comprovam a estabilidade do fluxo de Identidade.

## 18. Traceability
Esta evidência responde à análise documentada no Quality Risk Analysis, referenciada nos planos mestres de testes do AtlasBadge V1.0 e no Evidence Register, focando unicamente no risco positivo (Identity Preservation). Os caminhos negativos continuam acompanhados separadamente (QR-10).
