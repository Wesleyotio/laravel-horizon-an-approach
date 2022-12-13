# Uma abordagem para o Laravel Horizon

Se sua aplicação Laravel faz uso de filas e do [redis](https://redis.io/docs/about/), acrescentar o [Laravel Horizon](https://laravel.com/docs/master/horizon) pode ser uma opção interessante para trazer mais performasse e melhorar o gerenciamento dos jobs processados em sua aplicação.

**OBS:** Nesta abordagem fizemos uso do laravel 6.0 e horizon 3.7.2


## Dividir para conquistar
Ninguém melhor que você para saber quais filas possuem um  maior fluxo de entrada e quais possuem maior processamento, mas caso não tenha essa informação isso é só mais um motivo para usar o horizon, uma vez que ele gera métricas que sao apresentadas em um dashboard simples e intuitivo.

Para tirar o máximo de proveito do balanceamento do horizon é necessário agrupar suas filas da melhor forma possível, caso ainda não tenha entendido bem como esse balanceamento funciona sugiro que leia esse artigo do [Zechariah Campbell](https://medium.com/@zechdc/laravel-horizon-number-of-workers-and-job-execution-order-21b9dbec72d7) que explica de maneira bem detalhada.

Acredito que o auto balanceamento seja o modelo ideal para sistemas em que as filas tem picos de processamento em certos momentos do dia, assim mais processos são destinados para a fila com mais jobs em espera afim de zera ou deixa o numero de jobs em espera proximo aos das demais filas.

Por padrão nosso arquivo ***config/horizon.php*** o array de environments tem um formato semelhante ao da imagem a seguir

<img style=" width: 500px; 
    margin-left: auto;
    margin-right: auto;" src="./images/supervisor-1.png">

Se por exemplo as filas ***mail*** e ***images*** consomem muito tempo de processamento e possuem 500 jobs em espera e as demais filas estão zeradas, ambas terão mais processos para despacharem seus jobs. Em um dado momento a fila ***import*** ,que consome pouco tempo de processamento, tem um pico e fica com 3000 jobs em espera, neste momento parte dos processos sao descolocados para ***import*** afim de reduzi-la o quanto antes.

O problema na situação é que o balanceamento tornará a execução  das filas ***mail*** e ***images*** mais lento ainda por ter que deslocar processos para a fila ***import***, para resolver isso devemos dividir nosso supervisor-1 em dois, um responsável por executar os jobs das filas com tempo de processamento e quantidade elementos em espera menor.   