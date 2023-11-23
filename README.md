#include <iostream>
#include <fstream>
#include <string>
#include <vector>

class Atracao {
public:
    Atracao(std::string nome, double preco) : nome(nome), preco(preco) {}

    std::string getNome() const {
        return nome;
    }

    double getPreco() const {
        return preco;
    }

private:
    std::string nome;
    double preco;
};

class Ingresso {
public:
    Ingresso(const Atracao& atracao, std::string tipoIngresso)
        : atracao(atracao), tipoIngresso(tipoIngresso) {}

    double calcularPreco() const {
        return atracao.getPreco() * obterDesconto();
    }

    std::string obterDescricao() const {
        return "Ingresso para " + atracao.getNome() + " - " + tipoIngresso;
    }

private:
    const Atracao& atracao;
    std::string tipoIngresso;

    double obterDesconto() const {
        if (tipoIngresso == "Meia Entrada") {
            return 0.5;  // Meia entrada com 50% de desconto
        } else if (tipoIngresso == "Entrada com Desconto para Professores") {
            return 0.7;  // Desconto de 30% para professores
        } else if (tipoIngresso == "Entrada Inteira") {
            return 1.0;  // Sem desconto
        } else if (tipoIngresso == "Entrada de Estudante") {
            return 0.6;  // Desconto de 40% para estudantes
        }
        return 1.0;  // Valor padrão (sem desconto)
    }
};

class NotaFiscal {
public:
    NotaFiscal(std::string nomeCliente, std::string endereco, double total)
        : nomeCliente(nomeCliente), endereco(endereco), total(total) {}

    void imprimirNota() const {
        std::cout << "======== NOTA FISCAL ========" << std::endl;
        std::cout << "Cliente: " << nomeCliente << std::endl;
        std::cout << "Endereço: " << endereco << std::endl;
        std::cout << "Total: R$ " << total << std::endl;
        std::cout << "=============================" << std::endl;
    }

private:
    std::string nomeCliente;
    std::string endereco;
    double total;
};

int main() {
    // Crie as atrações
    Atracao arteModerna("100 Anos da Arte Moderna", 20.0);
    Atracao jogosOlimpicos("Jogos Olímpicos de Paris 2024", 25.0);
    Atracao santosDumont("150 Anos de Santos Dumont", 15.0);
    Atracao ia("Inteligência Artificial", 18.0);

    // Crie um vetor de atrações
    std::vector<Atracao> atracoes = {arteModerna, jogosOlimpicos, santosDumont, ia};

    for (const Atracao& atracao : atracoes) {
        // Exiba as opções de ingressos
        std::cout << "Selecione o tipo de ingresso para " << atracao.getNome() << ":" << std::endl;
        std::vector<std::string> tiposIngresso = {"Meia Entrada", "Entrada com Desconto para Professores", "Entrada Inteira", "Entrada de Estudante"};
        for (int i = 0; i < tiposIngresso.size(); ++i) {
            std::cout << i + 1 << ". " << tiposIngresso[i] << std::endl;
        }

        int escolhaTipoIngresso;
        std::cin >> escolhaTipoIngresso;

        if (escolhaTipoIngresso < 1 || escolhaTipoIngresso > tiposIngresso.size()) {
            std::cout << "Escolha de tipo de ingresso inválida." << std::endl;
            return 1;
        }

        // Crie o ingresso com base nas escolhas do usuário
        Ingresso ingresso(atracao, tiposIngresso[escolhaTipoIngresso - 1]);

        // Exiba o ingresso e o preço
        std::cout << "Ingresso adquirido: " << ingresso.obterDescricao() << std::endl;
        std::cout << "Preço: R$ " << ingresso.calcularPreco() << std::endl;

        // Solicite a forma de pagamento
        std::cout << "Selecione a forma de pagamento (1. Dinheiro, 2. Cartão): ";
        int escolhaFormaPagamento;
        std::cin >> escolhaFormaPagamento;

        // Calcular o total
        double total = ingresso.calcularPreco();
        if (escolhaFormaPagamento == 2) {
            total *= 1.1; // Adiciona 10% para pagamentos com cartão
        }

        // Emitir a nota fiscal
        std::cout << "Por favor, insira seu nome: ";
        std::string nomeCliente;
        std::cin.ignore(); // Limpa o buffer do teclado
        std::getline(std::cin, nomeCliente);

        std::cout << "Por favor, insira seu endereço: ";
        std::string enderecoCliente;
        std::getline(std::cin, enderecoCliente);

        NotaFiscal notaFiscal(nomeCliente, enderecoCliente, total);
        notaFiscal.imprimirNota();

        // Salvar a nota fiscal em um arquivo CSV
        std::ofstream arquivoCSV(atracao.getNome() + "_nota_fiscal.csv", std::ios::app);

        if (arquivoCSV.is_open()) {
            arquivoCSV << "Cliente," << nomeCliente << ",Endereço," << enderecoCliente << ",Total," << total << std::endl;
            arquivoCSV.close();
            std::cout << "Nota fiscal salva com sucesso." << std::endl;
        } else {
            std::cerr << "Erro ao abrir o arquivo CSV da nota fiscal." << std::endl;
        }
    }

    return 0;
}
