# Secrets
###### Solved by @Juh-s
>This is a CTF about Web Exploitation
## Sobre o desafio
We have several pages hidden. Can you find the one with the flag?

The website is running [here](http://saturn.picoctf.net:51099/index.html).

Hints: folders folders folders

**Tradução**:

Temos várias páginas ocultas. Você pode encontrar a que está com a flag?

O site está rodando [aqui](http://saturn.picoctf.net:51099/index.html).

Dica: pastas pastas pastas

## Solução
**Introdução**

Esse desafio envolve explorar arquivos de um site para encontrarmos a flag escondida, isso pode ser feito através da exploração de diretórios ocultos, arquivos referenciados no código principal e path traversal.

Ao usar o comando [curl](https://www.kali.org/tools/curl/) para checar a página e seu código-fonte, obtemos:

<img width="917" height="617" alt="image" src="https://github.com/user-attachments/assets/fc49f7e5-1ec0-4b35-98e0-62cce8f2d010" />

**Análise Inicial**

Logo ao rodar o comando no terminal, é possível observar a seguinte seção:

<img width="917" height="224" alt="image" src="https://github.com/user-attachments/assets/10a269d8-2984-42b0-9a22-d76e75be07bf" />

Essa parte do código diz que existe um diretório `/secret/` no servidor. Então o próximo passo, é tentar acessar esse diretório com o comando: `curl http://saturn.picoctf.net:51099/secret/`.

<img width="1230" height="252" alt="image" src="https://github.com/user-attachments/assets/fb1afe7c-eb66-4ab7-b4fc-7c3c5d576309" />

Dessa vez, o comando retornou esse código, dizendo: _Finally. You almost found me. you are doing well_, ou seja, o desafio nos mostra que quase encontramos a flag e que estamos progredindo bem na resolução.

Além disso, também está inserido nas linhas o seguinte: `<link rel="stylesheet" href="hidden/file.css" />`. Ou seja, existe um outro diretório `/secret/hidden/`.

Digitando novamente esse comando usando o novo diretório, o código mostrado é o seguinte:                                                                                                      
```
┌──(kali㉿kali)-[~]
└─$ curl http://saturn.picoctf.net:51099/secret/hidden/
<!DOCTYPE html>
<html>
  <head>
    <title>LOGIN</title>
    <!-- css -->
    <link href="superhidden/login.css" rel="stylesheet" />
  </head>
  <body>
    <form>
      <div class="container">
        <form method="" action="/secret/assets/popup.js">
          <div class="row">
            <h2 style="text-align: center">
              Login with Social Media or Manually
            </h2>
            <div class="vl">
              <span class="vl-innertext">or</span>
            </div>

            <div class="col">
              <a href="#" class="fb btn">
                <i class="fa fa-facebook fa-fw"></i> Login with Facebook
              </a>
              <a href="#" class="twitter btn">
                <i class="fa fa-twitter fa-fw"></i> Login with Twitter
              </a>
              <a href="#" class="google btn">
                <i class="fa fa-google fa-fw"></i> Login with Google+
              </a>
            </div>

            <div class="col">
              <div class="hide-md-lg">
                <p>Or sign in manually:</p>
              </div>

              <input
                type="text"
                name="username"
                placeholder="Username"
                required
              />
              <input
                type="password"
                name="password"
                placeholder="Password"
                required
              />
              <input type="hidden" name="db" value="superhidden/xdfgwd.html" />

              <input
                type="submit"
                value="Login"
                onclick="alert('Thank you for the attempt but oops! try harder. better luck next time')"
              />
            </div>
          </div>
        </form>
      </div>

      <div class="bottom-container">
        <div class="row">
          <div class="col">
            <a href="#" style="color: white" class="btn">Sign up</a>
          </div>
          <div class="col">
            <a href="#" style="color: white" class="btn">Forgot password?</a>
          </div>
        </div>
      </div>
    </form>
  </body>
</html>
```          

Nesse código, é possível observar uma linha em específico que referencia outro diretório: `<input type="hidden" name="db" value="superhidden/xdfgwd.html" />`, porém ao rodar isso no terminal ele nos devolve um erro.

<img width="702" height="155" alt="image" src="https://github.com/user-attachments/assets/e13a9565-eb6d-47f5-867f-d8e95e8f79cb" />

Isso porque esse não é o caminho certo, servindo apenas como uma distração já que ele não existe.

**Solução**

Para solucionar o desafio e encontrar a flag, basta tentar usar o comando seguindo apenas o diretório `/superhidden/` sem a parte `xdfgwd.html`. Isso porque em alguns desafios desse tipo, a flag está direto no diretório sem precisar acessar o arquivo apontado no HTML.

<img width="597" height="237" alt="image" src="https://github.com/user-attachments/assets/6598f1aa-6785-4d81-9f10-905519643e60" />

Feito isso, conseguimos obter a flag:

>`picoCTF{succ3ss_@h3n1c@10n_39849bcf}`

**Aplicação no dia a dia**

Desafios como esse simulam situações reais de segurança em aplicações web. Isso porque um indivíduo que queira atacar um servidor, pode encontrar arquivos sensíveis dos usuários ou do próprio site. Por isso, é importante identificar esses caminhos esquecidos para que eles não sejam acessados indevidamente e a segurança do servidor seja maior.
