# Logbook 3

## Desafio 1

Ao acedermos à página http://ctf-fsi.fe.up.pt:5001 encontramos um servidor Wordpress e após a navegação pelos vários links do mesmo descobrimos algumas das suas dependências tais como a versão do Wordpress (5.8.1) e os plugins instalados e as suas respetivas versões (Woocommerce plugin 5.7.1 e Booster for WooCommerce plugin 5.4.3), tal como sugeria o enunciado. 

Após procurarmos por CVE’s associados aos plugins e as suas respetivas versões enumerados anteriormente, encontramos o CVE-2021-34646. Após introduzir o CVE na flag o primeiro desafio foi completado. 

## Desafio 2

Para concluir o segundo desafio começamos por procurar o exploit da vulnerabilidade encontrada no primeiro desafio, consultando o site https://www.exploit-db.com/exploits/50299. Descobrimos então um script em python que nos permitia atacar o site e ao correr o código com o ID=1 obtivemos três links possíveis para o login no site. 

https://imgur.com/vNa9iMP

Um dos três resultados permitiu-nos fazer login na conta do administrador do site e posteriormente, após seguir as instruções do guião do CTF, descobrir a flag que faltava para concluir o desafio. 

https://imgur.com/D8N3bc6


