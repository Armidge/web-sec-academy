# Access Control

Access Control (Controle de Acesso) é o conjunto de mecanismos que determina quem pode acessar quais recursos e executar quais ações dentro de uma aplicação, aplicando os princípios de autenticação (quem é quem) e autorização (o que se pode fazer). Vulnerabilidades surgem quando essas verificações são ausentes, inconsistentes ou dependem de dados controláveis pelo usuário-cliente (IDs, parâmetros, headers), permitindo falhas como IDOR, Broken Access Control vertical/horizontal e **escalonamento de privilégios**, como exemplificado a diante. A validação deve ocorrer sempre no lado servidor, de forma centralizada e em cada requisição, *sem fé* em controles de interface ou em "segurança por obscuridade"¹.

### 📚 Referências e/ou leituras recomendadas:
- [OWASP Top 10 2021 – A01: Quebra de Controle de Acesso (pt-BR)](https://owasp.org/Top10/2021/pt-BR/A01_2021-Broken_Access_Control/)

---

## Lab 1: Unprotected admin functionality

> **Objetivo:** acessar o admin panel e deletar o user `Carlos`.

Nada de muito interessante no código-fonte dessa vez. Nenhum comentário, nenhum vestígio do que precisamos fazer ou acessar, sem pistas, a priori. Mas há um lugar em que geralmente, podemos encontrar alguma coisa, o `/robots.txt`!¹

E é no `/robots.txt` que encontramos isso:

```
User-agent: *
Disallow: /administrator-panel
```

Com acesso irrestrito ao panel admin, sem dificuldade, deletamos a conta do pobre Carlos.

O primeiro desafio, já foi!

(e é claro que, em `/my-account`, testei algumas credênciais como admin:admin ou administrator:administrator. Sem sucesso, rs.)

## Lab 2: Unprotected admin functionality with unpredictable URL

> **Objetivo:** acessar o admin panel e deletar o user `Carlos`.

Após uma rápida análise do código-fonte, temos o seguinte script, com um endereço que nos leva ao admin panel:

```JavaScript
var isAdmin = false;
if (isAdmin) {
   var topLinksTag = document.getElementsByClassName("top-links")[0];
   var adminPanelTag = document.createElement('a');
   adminPanelTag.setAttribute('href', '/admin-35a5zr');
   adminPanelTag.innerText = 'Admin panel';
   topLinksTag.append(adminPanelTag);
   var pTag = document.createElement('p');
   pTag.innerText = '|';
   topLinksTag.appendChild(pTag);
}
```

É, *user deleted successfully*, outra vez! Diferentemente do que vimos no primeiro desafio, esse panel admin estava com alguma obscuridade, com alguma segurança já que possuia caracteres aleatórios depois do `/admin-`, embora segurança, aqui, seja uma palavra muito forte (veja a primeira nota de rodapé). 

## Lab 3: User role controlled by request parameter

> **Objetivo:** continua o mesmo, acessar o admin panel e deletar o user `Carlos`.

Bem, agora temos um `/admin` acessivel, e algumas credênciais de acesso, como `wiener:peter` e `administrator:administrator` -- esta última, com os mesmos privilégios da primeira. Também sabemos que os cookies, aqui, podem ser modificados. Sem sucesso com path traversal para `/admin`, `/administrator` ou semelhantes.

Aos cookies, então! 🍪

[/img/access-control-1.PNG]

De `Admin=false` para `Admin=true`, quando logados em `wiener`, temos acesso ao painel de administrador. Mais privilegios, com uma simples alteração de cookie. Concluímos esse desafio.

Os cookies podem ser facilmente vistos e alterados a partir das ferramentas de desenvolvedor em qualquer navegador, de extensões como o Cookie-Editor ou de ferramentas como o [BurpSuite](https://portswigger.net/burp) e o [Caido](https://www.caido.io/).

## Lab 4: User ID controlled by request parameter, with unpredictable user IDs

> **Objetivo:** continua o mesmo, acessar o admin panel e deletar o user `Carlos`.

Bem, agora tem

---

¹ Segurança por obscuridade é a prática de proteger um sistema ocultando seus detalhes (códigos, estruturas, endpoints) em vez de usar **controles de segurança reais, mais robustos**. Informações que, dessa forma, são descobertas por enumeração, engenharia reversa, vazamentos, etc.

² O `robots.txt` é um arquivo na raiz do domínio que instrui crawlers sobre quais caminhos não devem ser indexados ou rastreados. Na prática, esse é um mecanismo, uma convenção entre bots que por vezes, acaba revelando caminhos mais ou menos sensíveis ou que alguém não gostaria que fossem vistos ou indexados por qualquer buscador como o Google.

## Acess-control.md --author

Por Jeyson S. Rodrigues (Armidge). | Email: [jsonsecurity@gmail.com](mailto:jsonsecurity@gmail.com) // 💬 Telegram: [@Armidge](https://t.me/Armidge)
