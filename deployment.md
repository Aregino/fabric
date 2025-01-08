# Pepiline de Implantação


### Objetivos

Tendo como objetivo criar um fluxo de desenvolvimento continuo, existe a opção de pepiline de implantação que consistem fazer un fluxo de deploy entre os ambientes, como exemplo utilizaremos fluxo, desenvolimento, test e produção (DEV, QAS e PROD).  


### Préquisitos
  - Workpaces na capacidade Premium
  - usuario ter permissão (administrador dos workpaces envolvidos)

Na estrutura do fabric em pepiline de implatação clicar em  + Novo Pepiline:

![image](https://github.com/user-attachments/assets/da59c34f-0911-462b-a98c-370f6f7a76cf)

vai aparecer uma tela para o nome e descrição, basta preenchere e cliar em avançar 

![image](https://github.com/user-attachments/assets/94db59fb-0669-4ffc-bae1-718eb7f80a5a)

Nesse ponto você coloca quantos ambientes vai criar o fluxo, no mínimo 02 podendo ser até 10.

![image](https://github.com/user-attachments/assets/c87db388-042f-466c-8f30-40c9fd29e096)

Na próxima tela o sistema exibe  os itens que configuravel para implatação:

![image](https://github.com/user-attachments/assets/d81ae668-5eab-4f7b-b397-ec7c2c6f0ddc)

Obs - Se clicar em Novos pepilines de implantação a iterface e muda:

![image](https://github.com/user-attachments/assets/25ca8b9f-cdac-44da-994f-237cd71be175)

- - um ponto importante é que se seu **workspace** estiver atrelado a outro pepiline ele, não poderá ser atribuido novamente.

![image](https://github.com/user-attachments/assets/cbfaab22-0783-4642-ac44-2da09d461559)



![image](https://github.com/user-attachments/assets/d2cef8b6-a6af-456c-800f-7ed60396c7cd)


Falhas que podem ocorrer:

Depois de feita a seleção dos itens que deseja fazer o deploy  o  processo executa de item a intem, e existe uma opção que você pode ignorar os possiveis erros  ou parar o processo caso ocorra um erro, nesse exemplo aqui optei por parar o processo:

Mensagem de erro:


![image](https://github.com/user-attachments/assets/b21de622-266e-4460-aa5a-2164b3ad75ed)


![image](https://github.com/user-attachments/assets/7f0ae2bf-abc2-437a-a192-f09132d15e82)

Ideal e tratar de correção, por alguma razão os metadados de conexão se perderam nesse caso corrigi manualmente e prossegui novamente com o deploy


# Considerações

Por meio da atribuição de um espaço de trabalho a um estágio de implantação: quando um espaço de trabalho é atribuído a um estágio de implantação, o pipeline de implantação tenta emparelhar os itens. Os critérios de emparelhamento são:

-- Nome do Item
-- Tipo de Item

Local da pasta: usado como desempate quando um estágio contém itens duplicados (dois ou mais itens com o mesmo nome e tipo)
O emparelhamento ocorrerá se um único item em cada estágio tiver o mesmo nome e tipo. Se houver mais de um item em um estágio com o mesmo nome e tipo, os itens serão emparelhados se estiverem na mesma pasta. Se as pastas não forem as mesmas, o emparelhamento falhará.

_Depois que os itens são emparelhados, renomeá-los não desemparelha os itens. Portanto, é possível que existam itens emparelhados com nomes diferentes._
