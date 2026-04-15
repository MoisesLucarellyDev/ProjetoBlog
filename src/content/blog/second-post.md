---
title: 'Experimentado IA localmente'
description: 'Testei rodar um modelo de IA no meu notebook'
pubDate: 'Apr 15 2026'
heroImage: '../../assets/blog-placeholder-3.jpg'
---

# Por que?

Como qualquer universitário médio eu estava acordado de madrugada scrollando infinitamente os feeds, em particular Youtube. Quando atualizei o feed me apareceu o podcast do Flow com o Fábio Akita(meu "héroi") falando sobre IA, e resolvi assisti. 

Nesse podcast em específico o Akita e o Igor conversaram muito sobre o uso de IA como ferramente e o Akita falou muito sobre teste/uso de modelos em local, na máquina, e isso me interessou bastante. Apesar dele comentar em especial sobre modelos que são relativamente pesados para máquinas normais e para ser humanos normais, eu quis ver na prática como o computador se comporta quando feito uma "pergunta" para o modelo. 


# Llma3

Pra isso eu fui atraz de um modelo leve, que fosse possível rodar nas minhas configurações(Ryzen 5500u, 16Gb RAM, SSD nvme 512Gb e linux Arch), e então encontrei o llma3 no Ollma que usa cerca de 4.7Gb de RAM.


Instalei o Ollma + llma3, conforme as instruções da página oficial. 

## Descobertas

### Consumo de RAM
Após instalar e apenas startar o servidor local da llma3 meu notebook já perdeu pouco mais de 4.7Gb ram, o que me deixou surpreso, pois eu achava que a RAM era consumida durante o processamento de uma pergunta, mas pelo que pesquisei, e isso me fez entender melhor o motivo do preço da RAM subir, o uso da RAM é feito pra alocar o modelo.

Imagino que isso se deva pelo fato de como a GPU/CPU precisão daqueles dados para fazer cálculos, e eles tem que ser feito de maneira rápida e precisa, é alocado não RAM pois é mais rápido para buscar. 

E com isso agradeci por não ter tentando testar uma variação do llma3 que é o llma3 70:b, que consome humildes **40Gb de RAM**!!!!

Teria como contornar essa falta de RAM, mas que não é nem um pouco eficiente.

#### "Solução": SWAP de memória

Pra contonar esse problema eu poderia, se quisesse, utilizar uma memória de SWAP, reservava uma quantidade massiva no SSD e tentava rodar, mas isso iria trazer um problemão que seria a **BRUTAL** queda de eficiência, e nem sei se quer rodaria, não testei.

Como minha RAM não tankaria "guardar" o modelo na RAM ele iria alocar o resto na SWAP, mas na hora que eu testasse o modelo ia ser perceptível a surra que meu notebook estaria tomando. A GPU/CPU iria precisar tanto ser utilizada para fazer o processamento das requisições quanto para ficar fazendo o SWAP, buscando e trazendo dado, e delegar mais uma função para o processador que já tá ocupado com os cálculos não é muito recomendável.

#### Grande sacada da Quantização

O llma3 tem cerca de 8 bilhões de parâmentros, levando em consideração que, a grosso modo, cada parâmetro pese 2bytes(``float16``), o modelo necessitaria de cerca de 15/16Gb de RAM, o que é praticamente tudo da memória do meu notebook. Mas como os cara que trabalham nisso são muito gênios, falo dos pesquisadores, não os donos de empresa que especulam coisas só pra vender, eles desenvolveram uma técnica chamada Quantização que meio que comprimi esses parâmetros,"espremem", para diminuir o quanto o ocupa na RAM, permitindo que caiba em máquinas mais modestas como a minha. 

Nesse exemplo do llama3 que eu usei a compresão deve ter sido de 16bits para 4bits de peso para cada parâmetro, ou seja, um redução para 1/4 do tamanho total.

Mas como nem tudo são flores, essa técnica tem um preço e quem paga ele é a CPU/GPU, tendo que descomprimir os dados compactados.

### Consumo da GPU/CPU

Minha APU já não é das melhores nem pra jogar, uma placa integrada vega 7, imagine para rodar um modelo de IA localmente. Mas eu testei e o coitado foi brutalmente molestado, mesmo usando um modelo leve no quesito RAM a GPU ainda vai ter muito trabalho pra calcular/processar a requisição.

Quando está em "stand-by" o modelo quase não tá consumindo nada, mas quando damos um empurrão o consumo explode pra 50-60%. 

Antes de falar o que acontece vou responder a pergunta que você acabou de fazer de "Ah, mas então tem processador sobrando, por que não usa tudo?", a CPU precisa deixar uma porcetagem livre pro computador não ficar totalmente inoperante naquele tempo que tá processando a requisição.

Quando a gente faz uma requisição, uma pergunta por exemplo, acontece uma matématica pesada por trás. Puro cálculo de matriz. Já fez multiplicação de matrizes? Se sim você sabe o trabalho que dá, imagine centanas, milhares e milhões desse tipo de cálculo sendo feitos, até a máquina pena pra isso, por isso é tão alto o consumo de CPU.

E como falei no tópico de Quantização, há um custo de processamento na descompressão dos dados, o que adiciona esforço e carga pro CPU.

## Conclusão

Queria muito poder testar o modelo do llma3 70:b. Um experimento bem legal seria usando processamento paralelo em multiplas máquinas em rede.

É muito interessente ver de maneira mais transparente o que acontece quando fazemos uma requisição para um IA, claro que é muito mais profundo que isso, mas só essa ponta do iceberg já é bem empolgante.

Por hoje é só, flw!