# WhoKnowsBot
<html>
  <h3> Overview do projeto</h3>
  <p>WhoKnowsBot é um robô social. Ele  foi desenvolvido como um estudo de caso sobre desenvolvimento de componentes de software associados a computação por humanos (human computation). O estudo é parte do projeto PIBIC-CNPq coordenado pelo prof. Lesandro Ponciano e conduzido pelo aluno bolsista Arthur Vinicius Soares, no curso Bacharelado em Sistemas de Informação da Pontifícia Universidade Católica de Minas Gerais (PUC Minas). O projeto foi executado entre Agosto de 2017 e Julho de 2018.</p>
 
 <p>De forma geral, o robô possui duas funcionalidades principais que podem ser acionadas pelo usuário: atribuição e agregação. Pela funcionalidade de atribuição (escalonamento), o usuário informa ao robô um tópico e o robô responde ao usuário quem (entre as pessoa que seguem o usuário no twitter) mais fala sobre aquele tópico no twitter. Pela funcionalidade de agregação, o usuário informa ao robô um tópico e o robô responde ao usuário quantas (entre as pessoa o usuário segue no twitter) falaram sobre aquele tópico no twitter. </p>
 
 <p>Há uma instância do robô ativa em https://twitter.com/whoknowsbot Informações sobre como conversar com o robô e explicações sobre as respostas dele estão em https://drive.google.com/file/d/1jhFCTByFLM2uOGsqa_BUB0BR9FarKnlV/view. <p>
 
 Abaixo estão mais informações sobre a implementação do robô.
 
  </tr>
  <h3> Configuração inicial do robô</h3>
  <p>Em twitterBotManage/twitter_connection.py atribuir valores às variáveis:</p>
  
  <pre>
    - consumerKey
    - consumerSecret
    - accessToken
    - accessTokenSecret
  </pre>
  
  <p>Essas informações estão disponíveis na página para desenvolvedores do Twitter: <br/>
  ex:</p>
  
  <pre>
    consumerKey = 'XXXXXXXXXXXXXXXXXXXXXXXXX'
    consumerSecret = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
    accessToken = '0000000000-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
    accessTokenSecret = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
  </pre>
  
  <p>A partir desse momento o script já está pronto para executar.<br/>
  Deve ser executado o arquivo <i>main.py</i>, responsável por chamar todos os métodos e arquivos durante a execução.<br/>
    Este script está em um loop ifinito. Quando necessário, deve-se interrompê-lo manualmente.<br/>
  </p>
  
  <br/>
  <h3>Ativação do script</h3>
  <p>Com o script executando, sempre que uma novas menções são direcionadas à conta do Twitter associado às variáveis definidas acima, inicia-se a análise de cada menção individualmente.<br/>
  OBS: Por conta de limites da API, a verificação de novas menções ocorrem de 1 em 1 minuto.
  </p>
  <p>Quando não há novas menções o script hiberna por 1 minutos, e verifica novamente novas menções<p>
  
  <br/>
  <h3>Análise das menções</h3>
  <p>O script é composto por 2 arquivos principais:</p>
  <pre>
    <i>manage_mentions.py</i>
    <i>analyze_mentions.py</i>
  </pre>
  
  <br/>
  <p><strong>Inicia-se chamando o arquivo <i>manage_mentions.py</i></strong></p>
  <p>A função <i>AnalyzeMentions()</i> é a primeira função a ser chamada no script.<br>
  Ela é responsável por chamar a função de coleta de novas menções, e determinar qual a próxima função a ser chamada de acordo com o conteúdo da menção.</p>
  <p>Para cada menção que é coletada, a string com seu conteúdo é quebrada, e verifica-se se o termo que o usuário utilizou. As opções padrões são limitadas ao uso dos termos QUEMSABE e QUANTOSSABEM.<br/>
A forma como será decidido qual fluxo seguir pode ser personalizada conforme o uso que será dado ao script.</p>
  
  <h3>Quantos sabem</h3>
  <p><u>Essa análise foca em descobrir quantos amigos falaram sobre um termo.</u></p>
  
  <p>É feita a coleta das publicações de quem o mencionador segue (amigos), que foram criadas até 7 dias precedentes ao início da análise e que possuem em seu conteúdo um termo especificado na menção. Através da função <i>GetFriendPostsTerm(term)</i> é retornado um vetor com as publicações.<br/>
  Cada item do vetor é iterado buscando alguma publicação com um termo especificado na menção.<br/>
  Ao final, verifica-se quantos amigos analisados usaram o termo especificado em alguma de suas publicações.</p>
  
  <p>Veja abaixo o diagrama de sequência que representa essa análise:</p>
  <img src="https://preview.ibb.co/mvy4So/image1.jpg" alt="image1" border="0">
  
  <h3>Quem sabe</h3>
  <p><u>Essa análise foca em descobrir quem é o melhor seguidor para responder algo sobre um termo.</u></p>
  
  <p>É feita a coleta das publicações de quem segue o mencionador (seguidores), que foram criadas até 7 dias precedentes ao início da análise e que possuem em seu conteúdo um termo especificado na menção. Através da função <i>GetFollowerPostsTerm(term)</i> é retornado um vetor com as publicações.<br/>
  Em seguida, busca-se a publicação mais antiga, que servirá de referência para selecionar o melhor seguidor.<br/>
  Para cada seguidor é feito o cálculo de uma pontuação que representa sua aptidão para responder alguma pergunta sobre o termo especificado. Sua pontuação aumenta a cada publicação que possui o termo, conforme a fórmula abaixo:</p>
  
  <img src="https://preview.ibb.co/cRjaHo/image4.png" alt="image4" border="0" height="60">
  
  <pre>
    - <b>Pw</b> é pontuação do seguidor w; 
    - <b>a</b> é publicação do seguidor w;  
    - <b>Tw,a</b> é o timestamp da publicação a; 
    - <b>T*</b> é o timestamp da publicação mais antiga
    - <b>now</b> é timestamp no momento da decisão de escalonamento (horário do sistema).
  </pre>
  
  <p>Ao final é retornado o seguidor com a melhor pontuação.</p>
  <p>Veja abaixo o diagrama de sequência que representa essa análise:</p>
  
  <img src="https://preview.ibb.co/dGL17o/image3.jpg" alt="image3" border="0">
  
  <h3>Respostas</h3>
  <p>Após a análise de cada menção, o mencionador é respondido, conforme o resultado, podendo ser:</br>
  Para análise do tipo QUANTOSSABEM:</p>
  <pre>
    - ReplyMentionHowMany(mentionId, user, usersQuantity) //Quando algum amigo falou sobre o termo
    - ReplyMentionNoneHowMany(mentionId, user) //Quando ninguém falou sobre o termo 
  </pre>
  <p>Para análise do tipo QUEMSABE:</p>
  <pre>
    - ReplyMentionWho(mentionId, user, candidate) //Quando algum seguidor foi escolhido
    - ReplyMentionNoneWho(mentionId, user) //Quando ninguém falou sobre o termo
  </pre>
</html>
