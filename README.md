# Uma abordagem para o Laravel Horizon

Se sua aplicação Laravel faz uso de filas e do [redis](https://redis.io/docs/about/), acrescentar o [Laravel Horizon](https://laravel.com/docs/master/horizon) pode ser uma opção interessante para trazer mais performasse e melhorar o gerenciamento dos jobs processados em sua aplicação.

**OBS:** Nesta abordagem fizemos uso do laravel 6.0 e horizon 3.7.2


## Dividir para conquistar
Ninguém melhor que você para saber quais filas possuem um  maior fluxo de entrada e quais possuem maior processamento, mas caso não tenha essa informação isso é só mais um motivo para usar o horizon, uma vez que ele gera métricas que sao apresentadas em um dashboard simples e intuitivo.

Para tirar o máximo de proveito do balanceamento do horizon é necessário agrupar suas filas da melhor forma possível, caso ainda não tenha entendido bem como esse balanceamento funciona sugiro que leia esse artigo do [Zechariah Campbell](https://medium.com/@zechdc/laravel-horizon-number-of-workers-and-job-execution-order-21b9dbec72d7) que explica de maneira bem detalhada.

Acredito que o auto balanceamento seja o modelo ideal para sistemas em que as filas tem picos de processamento em certos momentos do dia, assim mais processos são destinados para a fila com mais jobs em espera afim de zera ou deixa o numero de jobs em espera proximo aos das demais filas  