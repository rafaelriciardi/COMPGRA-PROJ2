Kevin Sakaguti Porto - 11039416
Rafael Riciardi Barreira - 11056916

# Seja bem vindo ao programa projeto2 -> "Montain View"

O programa é desenvolvido em C++, se trata de uma um mapa 3D que permite aos usuários navegarem e explorarem o mapa. Ele oferece a capacidade do usuário de navegar nas direções X, Z e Y. Os usuários podem navegar livremente, sem barreiras.

O projeto foi inspirado no "lookat", onde ele cria uma malha flat de triangulos para simular um mapa e permite a navegação em X e Z. Além de visualização de objetos estáticos.


## O Projeto:

Quando estavamos experimentando algumas modificações para o cenário, chegamos a um resultado bem legal substituindo a malha flat do lookat padrão pela implementação de uma malha baseada na função senoide, o que remeteu muito bem a uma simulação montanhas e lagos, que inclusive aperfeiçoamos com cores e outros métodos explicados abaixo. 
Ao ver o cenário gerado, nos lembramos prontamente do cenário gerado em Minecraft e assim, resolvemos implementar algumas funcionalidades de movimentação, como o "voar", muito utilizados no modo criativo desse jogo inspiração. Para melhorar a experiência, combinamos essa funcionalidade com novas implementações de movimentação de camera, para uma movimentação mais fluida.

## Definição do ambiente
### ALTERAÇÕES GROUND.CPP:

EM "void Ground::create(GLuint program)" foi alterado os parametros para diminuir o tamanho dos quadrados da malha.
```
  std::array<glm::vec3, 8> vertices{{{-0.1f, 0.0f, +0.1f},
                                     {-0.1f, 0.0f, -0.1f},
                                     {+0.1f, 0.0f, +0.1f},
                                     {+0.1f, 0.0f, -0.1f}}};
```
Dado esta alteração em "paint" tivemos que dividir as translações da malha por 5 também, pois da maneira que estava os quadrados das malhas estavam separados.
```
model = glm::translate(model, glm::vec3(x/5.0f, height, z/5.0f));
```
Para fazer as montanhas e lagos foi aplicado uma função senoide em relação a X e Z.
```
float height = amplitude * std::sin(frequency * x + phase) * std::sin(frequency * z + phase);
```
E como queriamos as alturas em função de 0.1 aplicamos a seguinte formula para a senoide se tornar discreta e não continua.
```
  height = std::round(height / 0.1f) * 0.1f;
```
por fim, adicionamos o parametro de altura ("height") na função de translação.
```
model = glm::translate(model, glm::vec3(x/5.0f, height, z/5.0f));
```
Para dar o aspecto de montanha aplicamos a cor verde no nas alturas medianas acima de 0, para os picos da montanha aplicamos a cor branca e para os lagos a cor azul, ainda no "paint"
```
if(height < 0){
        abcg::glUniform4f(m_colorLoc, 0.0f, 0.0f, 0.75f, 1.0f); 
      }else if (height > 0.7) {
        abcg::glUniform4f(m_colorLoc, 1.0f, 1.0f, 1.0f, 1.0f); 
      } else {
        abcg::glUniform4f(m_colorLoc, 0.0f, 1.0f, 0.0f, 1.0f); 
      }
```
### ALTERAÇÕES EM "onPaint":

Em onPaint, foi alterado a criação das figuras, colocamos cubos para serem observados. E estes cubos em constante rotação no eixo X e Y.
```
if(angle<360.0f) {
    angle = angle + 15.0f ;
  } else {
    angle = 0;
  }

  glm::mat4 model{1.0f};
  model = glm::translate(model, glm::vec3(0.0f, 0.5f, 0.0f));
  model = glm::rotate(model, glm::radians(angle), glm::vec3(1, 1, 0));
  model = glm::scale(model, glm::vec3(0.1f));
  abcg::glUniformMatrix4fv(m_modelMatrixLocation, 1, GL_FALSE, &model[0][0]);
  abcg::glUniform4f(m_colorLocation, 0.5f, -1.0f, 0.0f, 1.0f);
  abcg::glDrawElements(GL_TRIANGLES, m_indices.size(), GL_UNSIGNED_INT,nullptr);
```


## Definição da movimentação
Para a movimentação, decidimos que 


