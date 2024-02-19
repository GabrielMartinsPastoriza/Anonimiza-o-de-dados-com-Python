'''
SUGESTÃO PARA SOLUÇÃO DO ESTUDO DE CASO
Dicas Importantes: Nas linhas 54 até 58 É possível escolher qual técnica deseja aplicar descomentando a linha correspondente 
e comentando as outras duas. Além disso, as funções podem ser personalizadas de acordo com a necessidade ou, ainda, 
implementar outras técnicas de anonimização.

'''

import pyodbc
import random
import string

# Função para gerar uma string aleatória
def generate_random_string(length):
    letters = string.ascii_letters
    return ''.join(random.choice(letters) for _ in range(length))

# Função para mascarar um valor trocando caracteres por asteriscos
def mask_value(value):
    return '*' * len(value)

# Função para generalizar um valor trocando letras por X e números por 0
def generalize_value(value):
    generalized_value = ''
    for char in value:
        if char.isalpha():
            generalized_value += 'X'
        elif char.isdigit():
            generalized_value += '0'
        else:
            generalized_value += char
    return generalized_value

def anonimizar_dados():
    # Conectar ao banco de dados
    conn = pyodbc.connect(r'DRIVER={ODBC Driver 17 for SQL Server};SERVER={DESKTOP-M5RI9S5\SQLEXPRESS};DATABASE={PYSQL};UID=None;PWD=None;TRUSTED_CONNECTION=yes')
    cursor = conn.cursor()

    try:
        # Selecionar os registros da tabela 'contatos'
        cursor.execute("SELECT * FROM CLIENTES")
        registros = cursor.fetchall()

        # Anonimizar cada registro
        for registro in registros:
            # Selecionar a técnica de anonimização desejada
            # Neste exemplo, estamos usando a função mask_value para anonimizar o email
            cpf_anonimizado = mask_value(registro.CPF)  
            # Ou: campo_anonimizado = generalize_value(registro.campo)  

            # Atualizar o registro anonimizado na tabela 'contatos'
            cursor.execute("UPDATE CLIENTES SET CPF = ? WHERE ID = ?", (cpf_anonimizado, registro.ID))

        # Confirmar as alterações no banco de dados
        conn.commit()
        print("Dados anonimizados com sucesso!")

    except Exception as e:
        print(f"Erro ao anonimizar dados: {e}")

    finally:
        # Fechar a conexão com o banco de dados
        if conn:
            conn.close()

# Chamada da função para anonimizar os dados
anonimizar_dados()
