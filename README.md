# Projeto2 - Projeto de Computação Gráfica
## Lousa Mágica

Para esse segundo projeto para a disciplina de Computação Gráfica, foi feito uma LOusa Mágica que é possível desenhar através dos Sliders (Eixo_X e Eixo_Y) que movimentam o "lápis" nos eixos x e y.

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

Praticamente, esse arquivo não houve alterações ao window.hpp do Poligonos Regulares.

### window.cpp

Este arquivo possui os comandos para a implementação do programa. Abaixo será explicado as suas particularidades.

####  Window::onPaintUI

~~~C++
void Window::onPaintUI() {
  // Parent class will show fullscreen button and FPS meter
  abcg::OpenGLWindow::onPaintUI();

  // Our own ImGui widgets go below
  {
    // Window begin
    ImGui::Begin("Verificador de Triângulos: Lados e Ângulos");

    // 3 Sliders ao lado
    static std::array lados{0.0f, 0.0f, 0.0f};
    ImGui::SliderFloat3("Insira os lados", lados.data(), 0.0, 100.0);
  
    // Verioficação da existência de triângulos a partir dos lados inseridos dos sliders
    if ((lados[0] < lados[1] + lados[2]) && 
        (lados[1] < lados[0] + lados[2]) && 
        (lados[2] < lados[0] + lados[1])){
      ImGui::Text("Os lados inputados formam um triângulo\n");
    }
    else{
      ImGui::Text("Infelizmente, os lados inputados NÃO formam um triângulo\n");
    }

    //Criação de 2 Sliders, um abaixo do outro 
    static float angulo1{};
    ImGui::SliderFloat("Angulo1", &angulo1, 0.0f, 180.0f);

    static float angulo2{};
    ImGui::SliderFloat("Angulo2", &angulo2, 0.0f, (180.0 - angulo1));

    // A partir dos valores dos Sliders, são calculados e verificados os ângulos do triângulo, mostrando sua existencia, seu tipo e o valor do 3º ângulo
    if ((angulo1 == 0.0) || (angulo2 == 0.0) || (angulo1 + angulo2 >= 180.0)){
      ImGui::Text("Infelizmente, Não existe um triângulo com esses ângulos inputados, a soma dos dois ângulos inputados é >= a 180\n");
    }

    else{
      if ((angulo1 == angulo2) && (angulo1 != 60.0)){
      ImGui::Text("O seu triângulo é Isósceles, com os ângulos 1 e 2 iguais, e o ângulo 3 igual a %f\n", (180 - angulo1 - angulo2));
      }

      else if ((angulo1 == (180.0 - angulo2)/2) && (angulo1 != 60.0)){
        ImGui::Text("O seu triângulo é Isósceles, com os ângulos 1 e 3 iguais, e o ângulo 3 igual a %f\n", (180 - angulo1 - angulo2));
      }

      else if ((angulo2 == (180.0 - angulo1)/2) && (angulo2 != 60.0)){
        ImGui::Text("O seu triângulo é Isósceles, com os ângulos 2 e 3 iguais, e o ângulo 3 igual a %f\n", (180 - angulo1 - angulo2));
      }

      else if (angulo1 == 60.0 && angulo2 == 60.0){
        ImGui::Text("O seu triângulo é Equilátero, com todos os ângulos iguais a 60°\n");
      }

      else{
        ImGui::Text("O seu triângulo é Iscaleno, com todos os ângulos diferentes, e o angulo 3 igual a %f\n", (180 - angulo1 - angulo2));
      }

    }



    // Window end
    ImGui::End();
  }
  ~~~

Vamos ver com mais atenção cada parte do código:

~~~C++
abcg::OpenGLWindow::onPaintUI();
~~~
Acima temos a função membro onPaintUI da classe base, que mostra o medidor de FPS e o botão para alternar entre o modo janela e tela cheia

~~~C++
// Presente na linha 19 do arquivo window.cpp
ImGui::Begin("Verificador de Triângulos: Lados e Ângulos");

// Presente na linha 78 do arquivo window.cpp
ImGui::End();
~~~
Iniciamos e terminamos uma janela para conter todos os widgets que criarmos. Tudo que estiver entre o Begin e o End, estará dentro da janela.

~~~C++
// 3 Sliders ao lado
    static std::array lados{0.0f, 0.0f, 0.0f};
    ImGui::SliderFloat3("Insira os lados", lados.data(), 0.0, 100.0);
  
    // Verioficação da existência de triângulos a partir dos lados inseridos dos sliders
    if ((lados[0] < lados[1] + lados[2]) && 
        (lados[1] < lados[0] + lados[2]) && 
        (lados[2] < lados[0] + lados[1])){
      ImGui::Text("Os lados inputados formam um triângulo\n");
    }
    else{
      ImGui::Text("Infelizmente, os lados inputados NÃO formam um triângulo\n");
    }
~~~
Aqui iniciamos o widget que será um verificador de existência de triângulos. A partir dos valores inputados nos sliders é informado se existe ou não um triângulo com aqueles lados.

* Primeiramente, iniciamos um array estático chamado lados com valores floats, todos setados com zero.
* Através da classe `ImGui::SliderFloat3`, criamos 3 sliders horizontalmente consecutivos, que receberam os dados do array lados, tendo limite máximo de 100.
* Com a criação dos sliders, verificamos a existência do triângulo (utilizando estruturas condicionais `if` e `else`). Se os valores inputados nos sliders atenderem a desigualdade triangular, utilizando a classe `ImGui::Text`, imprimimos na janela o texto _Os lados inputados formam um triângulo_, caso contrário, ainda utilizando a classe `ImGui::Text`, imprimimos na janela o texto _Infelizmente, os lados inputados NÃO formam um triângulo_.

~~~C++
    //Criação de 2 Sliders, um abaixo do outro 
    static float angulo1{};
    ImGui::SliderFloat("Angulo1", &angulo1, 0.0f, 180.0f);

    static float angulo2{};
    ImGui::SliderFloat("Angulo2", &angulo2, 0.0f, (180.0 - angulo1));

    // A partir dos valores dos Sliders, são calculados e verificados os ângulos do triângulo, mostrando sua existencia, seu tipo e o valor do 3º ângulo
    if ((angulo1 == 0.0) || (angulo2 == 0.0) || (angulo1 + angulo2 >= 180.0)){
      ImGui::Text("Infelizmente, Não existe um triângulo com esses ângulos inputados, a soma dos dois ângulos inputados é >= a 180\n");
    }

    else{
      if ((angulo1 == angulo2) && (angulo1 != 60.0)){
      ImGui::Text("O seu triângulo é Isósceles, com os ângulos 1 e 2 iguais, e o ângulo 3 igual a %f\n", (180 - angulo1 - angulo2));
      }

      else if ((angulo1 == (180.0 - angulo2)/2) && (angulo1 != 60.0)){
        ImGui::Text("O seu triângulo é Isósceles, com os ângulos 1 e 3 iguais, e o ângulo 3 igual a %f\n", (180 - angulo1 - angulo2));
      }

      else if ((angulo2 == (180.0 - angulo1)/2) && (angulo2 != 60.0)){
        ImGui::Text("O seu triângulo é Isósceles, com os ângulos 2 e 3 iguais, e o ângulo 3 igual a %f\n", (180 - angulo1 - angulo2));
      }

      else if (angulo1 == 60.0 && angulo2 == 60.0){
        ImGui::Text("O seu triângulo é Equilátero, com todos os ângulos iguais a 60°\n");
      }

      else{
        ImGui::Text("O seu triângulo é Iscaleno, com todos os ângulos diferentes, e o angulo 3 igual a %f\n", (180 - angulo1 - angulo2));
      }

    }
~~~

Acima, temos o código onde é inputado 2 ângulos, e atráves desses ângulos é verificado a existência do triângulo, o seu tipo (isósceles, escaleno ou equilátero) e o 3º ângulo.

* Para começar, nos quatro primeiras linhas de código temos a criaçõ dos sliders verticamente distribuídos. Para cada slider é criado uma variável float estática (angulo1 e angulo2), com isso utilizamos a classe `ImGui::SliderFloat` para a criação dos sliders, passando seu "nome", onde será armazenado o valor, seu início e seu limite, exemplo para o slider do ângulo 1 (`ImGui::SliderFloat("Angulo2", &angulo1, 0.0f, 180.0f)`) seu nome é Angulo2, seu valor será armazenado na variável &angulo2,setado para começar em 0.0f com limite 180.0f. 

Um ponto que devemos notar é o limite do angulo2 (`ImGui::SliderFloat("Angulo2", &angulo2, 0.0f, (180.0 - angulo1))`). Aqui o seu limite depende do valor inserido no slider do angulo1. Então, conforme aumentamos o valor do angulo1, diminuímos o _range_ para o angulo2.

* Através de estruturas condicionais (`if`, `else if`e `else`), realizamos as verificações de existência, se o triângulo existe é verificado seu tipo e valor de seu 3º ângulo. E com a classe `ImGui::Text`, é impressa as mensagens.

####  Window::onCreate() e  Window::onPaint()

Essas classes não sofreram alterações. praticamente foram utilizadas as mesmas do programa firstapp.

**Observação:** Para a utilização dos sliders podemos fazá-la de duas maneiras: 
* Deslizando os sliders;
* Ou podemos navegar com as setas do teclado, e caso queira alterar algum valor, basta apertar o a tecla _enter_ e digitar o valor desejado.
