 Casa de Sucos API 

A ideia aqui foi criar uma API em Python com Flask para gerenciar os sucos de uma casa de sucos, conectando com a API pública Fruityvice(`https://www.fruityvice.com/api/fruit`) para puxar automaticamente as calorias e o açúcar de cada fruta (sempre considerando uma porção de 100 g por fruta).

 O que a API faz

- GET /frutas/<nome>: Consulta uma fruta na Fruityvice e devolve só o nome, calorias e açucar traduzidos para português.
- POST /sucos: Recebe o nome do suco, a lista de frutas e a descrição do sabor (tem_gosto_de), consulta os valores na Fruityvice, soma tudo e salva o suco.
- GET /sucos: Mostra todos os sucos que já foram cadastrados até o momento.
- PUT /sucos/<id> (Desafio bônus): Permite trocar as frutas de um suco pelo id e já recalcula as calorias e o açúcar automaticamente.
- Tratamento de erros: Se você digitar uma fruta que não existe (ou escrever em português em vez de inglês, já que a Fruityvice usa nomes em inglês), a API responde com erro 404 e uma mensagem explicando o que aconteceu.

 Como rodar o projeto na máquina

 Clonar o repositório e entrar na pasta
bash
git clone SEU_LINK_DO_REPOSITORIO_AQUI
cd casa-de-sucos-api

 Criar e ativar o ambiente virtual (.venv)
No Windows (Git Bash):
bash
python -m venv .venv
source .venv/Scripts/activate

(Se estiver usando PowerShell, use .venv\Scripts\Activate.ps1)

 Instalar as bibliotecas necessárias
bash
pip install -r requirements.txt

Subir a API
bash
python app.py

Depois disso, a API já vai estar rodando em http://127.0.0.1:5000 e a interface do Swagger pode ser acessada direto no navegador em:
http://127.0.0.1:5000/docs

!Os sucos ficam salvos em memória enquanto o servidor estiver ligado. Se parar e rodar o app.py de novo, a lista começa zerada.


## Testes realizados no Swagger (/docs)

Cenários testados diretamente pela interface do Swagger para validar cada requisito da atividade:

Consultando uma fruta que existe (GET /frutas/banana)
-Status: 200 OK
-Retorno:
json
  {
    "acucar": 12,
    "calorias": 89,
    "nome": "Banana"
  }


Testando fruta inexistente para validar o erro (GET /frutas/jabuticaba_invalid)
-Status:404 Not Found
-Retorno:
json
  {
    "mensagem": "Putz, não achei a fruta 'jabuticaba_invalida' na base da Fruityvice. Confere se o nome está em inglês (ex: banana, strawberry)."
  }

Cadastrando um suco novo (POST /sucos)
-JSON enviado:
json
  {
    "nome": "Vitamina Tropical",
    "frutas": ["banana", "strawberry"],
    "tem_gosto_de": "Tamarindo"
  }

- Status: 201 Created
-Retorno (soma automática de 100 g de banana + 100 g de morango):
  json
  {
    "frutas": ["banana", "strawberry"],
    "id": 1,
    "nome": "Vitamina Tropical",
    "tem_gosto_de": "Tamarindo",
    "total_acucar": 16.89,
    "total_calorias": 121
  }
  

Listando os sucos cadastrados (GET /sucos)
-Status:200 OK
-Retorno:Lista em JSON contendo o suco cadastrado no passo anterior e seus valores nutricionais.

 Atualizando as frutas do suco (PUT /sucos/1)
-JSON enviado:
  json
  {
    "frutas": ["banana", "mango"]
  }
  
- Status: 200 OK
- Retorno: O suco id: 1 atualizado com a nova lista de frutas e os novos totais de calorias e açúcar recalculados.


Prints das evidências
- ![Teste GET fruta](evidencias/get-fruta.png)
- ![Teste erro 404](evidencias/erro-404.png)
- ![Teste POST suco](evidencias/post-suco.png)
- ![Teste GET sucos](evidencias/get-sucos.png)
- ![Teste PUT suco](evidencias/put-suco.png)
