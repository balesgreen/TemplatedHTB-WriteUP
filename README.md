<h1> Write-up Templated HTB </h1>

Bom, fazendo um Write-up do Templated HTB após eu ter resolvido o CTF.

Primeiramente após iniciar a maquina, resolvi fazer um scan de portas com o nmap, mas... sem sucesso. Nenhuma porta foi retornada, portanto parti para a Web e foi
retornado uma página com algumas mensagens.

<img src="https://imgur.com/QBypuFx.png">

Logo após essas mensagens, resolvi procurar por alguns diretórios e um deles era /admin/, porém sem sucesso.
Logo isso, percebi que a pagina retornava em escrito tudo o que eu digitava no URL.

<img src="https://imgur.com/lx1MZzX.png">

Bom, logo percebi que eu poderia injetar algum tipo de code JavaScript para ver se me retornava alguma modificação diretamente do site.
Tentei utilizar o famoso payload xss ```<script>alert("xss")</script>``` mas sem sucesso, o site me exibia uma mensagem diferenciada.

<img src="https://imgur.com/iXSYQGR.png">

Então fiquei muito sem o que fazer pois eu não conseguia ir adiante executando alguns tipos de payload XSS.
Pesquisei um pouco mais sobre algumas payloads diferenciadas de XSS e resolvi testa-las para ver se eu conseguia executar alguma delas.
Bom... para a infelicidade daqueles que torciam contra, encontrei uma payload que funcionou perfeitamente executando o code.

```<img src=x onerror=alert(1)>```

<img src="https://imgur.com/uNZEwaD.png"> 

Great! Rooted? No!

Perfeito, primeiro passo concluido. Fazendo uma análise um pouco maior sobre a index inicial do site, observei a mensagem Flask/Jinja2
e abri mão de pesquisar um pouco mais sobre essa mensagem e percebi que se tratava de um mecanismo de Template. Bom, aqui começa a caçada
de verdade! 

Tendo em mente que Jinja2 se tratava de um mecanismo de template, resolvi então pensar um pouco além da caixola. Entrei em uma conclusão
de que se tratava de um Template, então eu poderia executar alguns payloads SSTI.

Então resolvi executar o famoso payload ```{{10+10}}```.

<img src="https://imgur.com/ZFsXkNa.png">

Oh yeah, its perfect!

O payload executou, obtivo um resultado de 10+10 = 20! Isso é perfeito! Agora eu precisei entender um pouco mais para saber que o Jinja2 executava
alguns comandos em python através do URL. Foi ai que eu resolvi pesquisar um pouco mais sobre os famosos artigos do Jinja2 e percebi que utilizando
```_mro__``` ou ```mro()``` eu conseguia voltar a raiz de objetos herdados no ambiente Python atual.

Então pesquisando mais sobre payloads, encontrei este payload: ```{{"".__class__.__mro__[1].__subclasses__()[186].__init__.__globals__["__builtins__"]["__import__"]("os").popen("echo Hello! *").read()}}```
e após a execução deste payload, obtive uma resposta!

<img src="https://imgur.com/SYDVanA.png">

Uau! Consegui listar alguns arquivos que estavam dentro do servidor e por final, podemos notar logo ali o ```flag.txt```!

Executando um ```cat flag.txt``` diretamente na nossa payload, conseguimos pegar a flag e contar mais um ponto!

<img src="https://imgur.com/9yHDZ2y.png">

Perfeito! CTF concluido! Espero que vocês tenham gostado desse Write-up e até o próximo!

Até breve, gente! 

Abraços do Mark!
