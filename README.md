# Orientação a Objetos

- **Coesão:** Atributos e métodos dentro de uma classe devem estar em harmonia, ou seja, fazer parte de um mesmo contexto
- **Encapsulamento:** 
  - Proteger a classe de manipulações externas
  - Vai além de getter e setter, deve expor somente o que for necessário e manter as manipulações sob controle da própria classe
- **Acoplamento:** Uma classe depende da outra. Acoplamento vai ocorrer, mas o acoplamentos muito fortes devem ser evitados
  - Exemplo: Classe A recebe uma lista da classe B e faz um loop nessa lista para calcular o valor. Se a classe B mudar a implementação dessa lista, vai afetar a classe A. O ideal nesse caso era ter um método que retorna o valor calculado
- **Polimorfismo:** Duas ou mais classes derivadas de uma mesma superclasse podem invocar métodos que têm a mesma assinatura, mas comportamentos distintos
- **Herança:** Equivalente ao "é um". Quando uma classe (filha) é uma variação de outra (pai).
- Exemplo: Pessoa Física e Pessoa Jurídica extendem de Pessoa
- **Composição:** Equivalente ao "tem um". Quando uma classe tem outra classe como propriedade.

# SOLID

- **Single Responsibility Principle:** Classe deve fazer apenas uma coisa específica
- **Open / Closed Principle:** Aberto para extensão, fechado para mudanças. Codificar de modo que facilite polimorfismo / extends / adição de comportamentos, sem necessidade de alterar a classe em questão
  - Exemplo: Uma classe que faz várias validações. Em vez de validar em um método dentro da classe, pode ter cada validação em uma classe específica implementando uma interface e a classe principal recebe uma lista da interface para percorrer e fazer as validações. No caso de uma nova validação, não será necessário alterar a classe principal, basta incluir um novo validador na lista.
- **Liskov Substitution Principle:** Usar herança somente quando de fato se trata de uma variação da classe pai
  - Exemplo: Um pato de brinquedo pode parecer, mas NÃO é um pato (animal), portanto, não deve herdar seus comportamentos
  - Recomendação: Substituir herança por composição
- **Interface Segregation Principle:** Uma classe não deve implementar um método que não utilizará
  - Recomendação: Segregar as lógicas em interfaces mais específicas para que a classe implemente apenas as interfaces realmente necessárias
- **Dependency Inversion Principle:** Depender de abstrações (interface) que são mais estáveis em vez de implementações

# Microsserviços

- **Conceito**: Cada serviço cuida de um contexto específico da aplicação com seu próprio estado (repositório) e se comunica com as demais aplicações através de REST, eventos etc...
- **Service Register**: Aplicação em que as instâncias dos microsserviços se registram. A partir disso, os demais serviços conseguem se redirecionar apontando apenas para o applicationName, abstraindo o endereço. **Implementação no Spring: Eureka**
- **Config Server**: Centraliza o controle das configurações (properties). É possível guardar no disco ou github. **Implementação no Spring: ConfigServer**
- **Load Balancer**: Distribui a carga de requisições entre multiplas instâncias. **Implementação no Spring: Client Side Load Balancing (Netflix Ribbon)**
- **Spring Feign**: Mais fácil criar HTTP Client. Abstrai as requisições para outros serviços (RestTemplate). É integrado aos demais serviços do Spring Cloud
- **Distributed tracing**: Centralizar os logs em um local (Papertrail e Kibana por exemplo) através de um appender que publica os log nessas ferramentas como se fossem eventos. **Implementação no Spring: Spring Sleuth**
- **Circuit Breaker**: Quando a chamada para um outro microsserviço falha, um método de fallback é chamado para evitar diverssas falhas seguidas (cirquito fechado). Esse método pode ter uma mensagem de erro, ou um retorno cacheado etc... Após um determinado período de tempo, ele tenta chamar novamente o serviço para verificar se voltou a funcionar. **Implementação no Spring: Hystrix**.
- **Bulkhead**: Divide um conjunto de threads em cada parte da aplicação. Dessa forma, uma lentidão em uma parte, não afeta todo o restante.
- **Tratamento de Erro**: O ideal é que o microsserviço va mantendo o estado do dominio conforme seu ciclo de vida avance através de outros microsserviços. Dessa forma, caso tenha algum erro no meio do caminho é possível fazer um reprocessamento ou cancelar por exemplo. Esse controle pode ser feito automáticamente pela aplicação de acordo com alguma regra, ou pelo usuário.
- **API Gateway**: Ponto central das requisições que redireciona para um determinado serviço de acordo com o path. Já é integrado com o Eureka para identificar os endereços. **Implementação no Spring: Spring Zuul**
- **Segurança**: Pode criar um serviço de autenticação que implemente o spring security e oauth2. O client (escopo web e mobile por exemplo) se autentiquem como aplicação em seu escopo e junto enviem usuário e senha do usuário por exemplo. Uma vez autenticado, o serviço de segurança retorna um token que deve ser repassado para os microsserviços na requisição. Os microsserviços por sua vez buscam no serviço de segurança a partir do token, quais as roles a requisição possui e por consequência, quais suas autorizações. **Implementação no Spring: Spring Security e Spring Cloud Oauth2**. É importante habilitar a passagem dos Headers no Zuul (zuul.sensitive-headers).
