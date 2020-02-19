using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.SqlClient;

namespace BibliotecaSenac_GustavoB
{// INICIO NAMESPACE
    class Program
    {// INICIO CLASSE
        private static int authorId;
        private static object author;

        static void Main()
        {//INICIO METODO


            Console.WriteLine("                                                                                                                     ");
            Console.WriteLine("=====================================================================================================================");
            Console.WriteLine("                                                 Biblioteca Senac                                                   ");
            Console.WriteLine("=====================================================================================================================");

            Console.WriteLine("O que Você deseja fazer: ");
            Console.WriteLine("(0) - Consulta de livro");
            Console.WriteLine("(1) - Cadastro de livro");
            Console.WriteLine("(2) - Remoção de livro");
            Console.WriteLine("(3) - Atualizar o livro");


            const string connectionString = "Server = (localDb)\\MSSQLLocalDB; Database = Biblioteca_Senac_GustavoBD3; Integrated Security = True";
            var selection = int.Parse(Console.ReadLine());

            if (selection == 0)
            {
                string queryString = "SELECT Books.Id, Books.Name, Authors.Name FROM Books, Authors WHERE Books.Author = Authors.Id ";
                using (SqlConnection connection = new SqlConnection(connectionString))//Inicia uma instancia da classe SqlConnection passando   
                                                                                      // a connectionString como parametro
                {
                    connection.Open();//cria um commando SQL que sera executado no banco de dados, passando a conexão abre a conexão que ja foi criada
                    SqlCommand command = new SqlCommand(queryString, connection);

                    using (SqlDataReader reader = command.ExecuteReader())
                    {
                        while (reader.Read())
                        {
                            Console.WriteLine(String.Format("{0}, {1}, {2}",
                                reader[0], reader[1], reader[2]));
                        }
                    }
                }

                Console.WriteLine("Aperte uma tecla para continuar ...");
                Console.ReadKey();
                Console.Clear();
                Program.Main();
            }


            if (selection == 1)
            {
                Console.WriteLine("Qual o nome do livro que você deseja cadastrar");
                var book = Console.ReadLine();
                Console.WriteLine("Qual o nome do autor do livro");
                var author = Console.ReadLine();

                //passo 1 - cadastrar o autor

                using (var conn = new SqlConnection(connectionString))
                {
                    conn.Open();
                    var authorInsert = new SqlCommand(string.Format
                        ("INSERT INTO Authors (Name) VALUES ('{0}')", author), conn);
                    authorInsert.ExecuteNonQuery();
                    conn.Close();
                }
                // Passo 2 - Consultar o autor que acabou de ser inserido
                using (var conn = new SqlConnection(connectionString))
                {
                    conn.Open();
                    var authorQuery = new SqlCommand("SELECT MAX(Id) FROM Authors", conn);
                    var authorRead = authorQuery.ExecuteReader();
                    while (authorRead.Read())
                    {
                        authorId = authorRead.GetInt32(0);
                    }
                    conn.Close();


                }
                //passo 3 - inserir o livro

                using (var conn = new SqlConnection(connectionString))
                {
                    conn.Open();
                    var command = new SqlCommand(
                        string.Format("INSERT INTO Books (Name, Author) VALUES ('{0}', {1})", book, authorId),
                        conn);
                    var res = command.ExecuteNonQuery();
                    conn.Dispose();

                    if (res > 0)
                    {
                        Console.WriteLine("O livro foi inserido com sucesso");
                    }
                    else
                    {
                        Console.WriteLine("Houve um problema ao inserir o livro no banco de dados");
                    }
                    Console.WriteLine("Aperte uma tecla para continuar ...");
                    Console.ReadKey();
                    Console.Clear();
                    Program.Main();
                }


            }
            if (selection == 2)
            {
                Console.WriteLine("Insira o livro que você deseja excluir: ");
                var bookToDelete = int.Parse(Console.ReadLine());


                using (var conn = new SqlConnection(connectionString))
                {
                    conn.Open();
                    var BookRemove = new SqlCommand(string.Format
                        ("DELETE FROM Books WHERE Id = {0}", bookToDelete),
                        conn);
                    var res = BookRemove.ExecuteNonQuery();
                    conn.Close();

                    if (res > 0)
                    {
                        Console.WriteLine("O livro foi deletado com sucesso ");
                    }
                    else
                    {
                        Console.WriteLine("Houve um erro ao deletar o livro do banco de dados ");
                    }


                    Console.WriteLine("Aperte uma tecla para continuar ...");
                    Console.ReadKey();
                    Console.Clear();
                    Program.Main();
                }


            }
            if (selection == 3)
            {
                Console.WriteLine("Insira o Id do Livro que você deseja atulizar");
                var bookToUpdate = int.Parse(Console.ReadLine());

                Console.WriteLine("Insira o nome atualizado do livro: ");
                var newBookName = Console.ReadLine();
                using (var conn = new SqlConnection(connectionString))
                {
                    conn.Open();
                    var bookUpdate = new SqlCommand(string.Format
                        ("UPDATE Books SET Name = '{1}' WHERE Id = {0}", bookToUpdate, newBookName),
                        conn);
                    var res = bookUpdate.ExecuteNonQuery();
                    conn.Close();

                    if (res > 0)
                    {
                        Console.WriteLine("O livro foi atualizado com Sucesso ");
                    }
                    else
                    {
                        Console.WriteLine("Houve um erro ao atualizar o livro no banco de dados ");
                    }
                    Console.WriteLine("Aperte uma tecla para continuar ...");
                    Console.ReadKey();
                    Console.Clear();
                    Program.Main();
                }

            }


        }// INICIO METODO
    }// FIM CLASSE
}// FIM NAMESPACE

// EU NUNCA CRIO UM METODO DENTRO DE OUTRO METODO 
// UM METODO SERVE PARA REALIZAR UMA AÇÃO E TAMBEM COMO PONTO DE CHAMADA
// o que identifica unicamente os registro no banco de dados é uma chave primaria 
