# Super Serial
###### Solved by @Juh-s
>This is a CTF about Web Exploitation
## Sobre o desafio

Try to recover the flag stored on this website http://mercury.picoctf.net:14804/

Hints: The flag is at ../flag

**Tradução**:

Tente recuperar a flag armazenada neste site http://mercury.picoctf.net:14804/

Dica: A flag está em ../flag

## Solução
**Introdução**

Ao entrar no site, temos essa tela inicial:

<img width="1767" height="550" alt="image" src="https://github.com/user-attachments/assets/09abceb7-5ad1-4cf1-bc4a-35cd7fad02a0" />

Inicialmente, é necessário ver o código do servidor para entender melhor seu funcionamento. Ao fazer isso, descobrimos a vulnerabilidade certa a ser explorada.

<img width="1875" height="671" alt="image" src="https://github.com/user-attachments/assets/bc4e2b47-5ede-4db9-987a-f21d07a1f79b" />

Além disso, iremos procurar por `robots.txt`, um arquivo público que sites usam para dizer quais páginas não devem ser indexadas. Isso porque podemos encontrar alguma informação que não aparece no menu principal.

<img width="540" height="127" alt="image" src="https://github.com/user-attachments/assets/e28bf9ec-0e25-43a0-9684-8e457fa28386" />

Dessa forma, encontramos um arquivo `.phps` que é importante para solucionarmos o desafio.

**Análise Inicial**

Ao fazer a análise do arquivo `robots.txt`, é possível observar um arquivo `.phps`, uma extensão que alguns servidores usam para mostrar o código-fonte [PHP](https://www.php.net/manual/pt_BR/index.php) em texto, em vez de executar o PHP. Ou seja, ao fazer a análise do `index.phps`, conseguimos ver o código que normalmente só o servidor tem acesso.

<img width="1861" height="962" alt="image" src="https://github.com/user-attachments/assets/576afa84-a1ef-4278-8f27-aa42462a26b5" />

Ao fazer a análise do código, esse trecho chama a atenção:

```
<?php
require_once("cookie.php");

if(isset($_POST["user"]) && isset($_POST["pass"])){
	$con = new SQLite3("../users.db");
	$username = $_POST["user"];
	$password = $_POST["pass"];
	$perm_res = new permissions($username, $password);
	if ($perm_res->is_guest() || $perm_res->is_admin()) {
		setcookie("login", urlencode(base64_encode(serialize($perm_res))), time() + (86400 * 30), "/");
		header("Location: authentication.php");
		die();
	} else {
		$msg = '<h6 class="text-center" style="color:red">Invalid Login.</h6>';
	}
}
?>
```
Isso porque podemos ver mais dois arquivos a serem analisados. O primeiro que é o `cookie.php`, que não acessa o arquivo diretamente então é preciso mudar para `.phps`:

<img width="802" height="921" alt="image" src="https://github.com/user-attachments/assets/56157ab1-d0a4-453f-beb1-5433fcb47a6f" />

E o segundo arquivo, `authentication.php` que também é necessário mudar para `.phps`:

<img width="1850" height="907" alt="image" src="https://github.com/user-attachments/assets/9b0446e4-3466-4f8c-bcbc-4aacec6796d3" />

No arquivo `cookie.phps` conseguimos observar que o servidor pega o cookie "login" e faz `$perm = unserialize(base64_decode(urldecode($_COOKIE["login"])));`
e depois deserializa dados controlados externamente, usando `$perm->is_guest()` e `$perm->is_admin()`. Também é importante entender que quando o código chama `$perm->is_guest()` e essa função não existe no objeto real, um erro será lançado e então o código captura o erro e faz `die("Deserialization error. " .$perm)`. Ao concatenar o objeto `$perm` com string, PHP chama `__toString()` e o conteúdo do arquivo apontado por `log_file` é incluído na resposta.

Além disso, no `authentication.phps` aparece uma classe `access_log` que define um método `__toString()` que irá ler o arquivo apontado por `$this->log_file`.

Isso tudo é importante porque se aproveitarmos que `$perm` seja um objeto `access_log` com a propriedade `log_file` igual a `../flag` (que foi dada na dica), então:

* `unserialize()` cria o objeto `access_log` com `log_file = "../flag"`.
* O código gera o erro causado pelo `$perm->is_guest()`.
* O bloco `die(...)` printa `$perm`.
* Ao converter `$perm` para string, `__toString()` é executado e retorna o conteúdo de `../flag`, que aparece na resposta HTTP.

**Solução**

<img width="1422" height="381" alt="image" src="https://github.com/user-attachments/assets/f16ddd3a-9542-4885-a48f-58994ef3b4fd" />


Feito isso, basta atualizar a página e o site retornará:

<img width="727" height="101" alt="image" src="https://github.com/user-attachments/assets/0fcc344b-4936-4725-b0e9-2ba044cce3cf" />

>`picoCTF{th15_vu1n_1s_5up3r_53r1ous_y4ll_261d1dcc}`

**Aplicação no dia a dia**
