# Projeto2 - Projeto de Computação Gráfica
## Lousa Mágica

Para esse segundo projeto para a disciplina de Computação Gráfica, foi feito uma Lousa Mágica (baseado no código fonte dos Polígonos Regulares apresentado em sala de aula) que é possível desenhar através dos Sliders (Eixo_X e Eixo_Y) que movimentam o "lápis" nos eixos x e y.

Também é possível aumentar o tamanho da ponta do "lápis" através do slider Tamanho.

## Implementação

### main.cpp

Praticamente, esse arquivo não houve alterações ao main.cpp do Poligonos Regulares. Somente houve mudança no nome da página, presente na linha 12:

~~~C++
Window window;
    window.setOpenGLSettings({.samples = 2, .doubleBuffering = false});
    window.setWindowSettings({
        .width = 600,
        .height = 600,
        .title = "Lousa Magica",
    });
~~~

### window.hpp

Praticamente, esse arquivo não houve alterações ao window.hpp do Poligonos Regulares. SHouve a inclusão de 3 variáveis gloabais que serão utilizadas nos sliders para alterar os valores de deslocamento nos eixos x e y (presentes nas linhas 33 e 35) iniciados na posição (0,0), e o tamanho do "lápis" iniciados com tamanho 0,01 (presente na linha 31).

~~~C++
  float escala{0.01};

  float eixoX{0.0};

  float eixoY{0.0};
~~~

ALém disso, o valor inicial do delay para escrever na tela foi setado para zero (0), conforme linha 27 no código.

~~~C++
  int m_delay{0};
~~~


### window.cpp

Este arquivo possui os comandos para a implementação do programa. Abaixo será explicado as suas particularidades.

####  Window::onPaintUI

Foi alterada o tamanho da caixa que contem todos os widget

~~~C++
    auto const widgetSize{ImVec2(200, 150)};
~~~

Foi criado 3 sliders do tipo float para controlar as movimentações os eixos e o tamanho do "lápis".
Para os eixos, forão estipulados os valores de limites inferior (-1) e superior (1).
Para o tamanho, forão estipulados os valores de limites inferior (0,01) e superior (0,25).
~~~C++
    //SLider para controlar tamanho do "lápis"
    ImGui::SliderFloat("Tamanho", &escala, 0.01f, 0.25f);
    
    //SLidera para controlar os eixos x e y
    ImGui::SliderFloat("Eixo_X", &eixoX, -1.0f, 1.0f);

    ImGui::SliderFloat("Eixo_Y", &eixoY, -1.0f, 1.0f);
~~~

#### Window::onPaint

Primeiramente criamos um lápis (com 30 lados) através da composição de triângulos lado a lado:

~~~C
 // Criação de um lápis com 30 lados
  auto const sides{intDist(m_randomEngine)};
  setupModel(sides);
~~~

Aqui é utilizado as variáveis globais de posição que serão alteradas através dos sliders criados no onPaintUI.

~~~C++
  // Altera a posição em relação ao (0,0)
  glm::vec2 const translation{eixoX, eixoY};
  auto const translationLocation{
      abcg::glGetUniformLocation(m_program, "translation")};
  abcg::glUniform2fv(translationLocation, 1, &translation.x);
~~~

Aqui é utilizado a variável global de tamanho que será alterada através dos slider criados no onPaintUI
~~~C++
  // ALtera o tamanho do lápis
  auto const scale{escala};
  auto const scaleLocation{abcg::glGetUniformLocation(m_program, "scale")};
  abcg::glUniform1f(scaleLocation, scale);
~~~

Baixo temos a cor de preenchimento do lápis:

~~~C++
  // Cor de preenchimento do lápis
  glm::vec3 const color1{0.5f, 0.5f, 0.5f};
  glm::vec3 const color2{0.5f, 0.5f, 0.5f};
 ~~~
 
**Observação:** Para a utilização dos sliders podemos fazá-la de duas maneiras: 
* Deslizando os sliders;
* Ou podemos navegar com as setas do teclado, e caso queira alterar algum valor, basta apertar o a tecla _enter_ e digitar o valor desejado.
