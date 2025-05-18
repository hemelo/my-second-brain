## 📖 **Introdução**

Comentários bem escritos são essenciais para garantir a **manutenibilidade**, **legibilidade** e **entendimento** do código, especialmente em projetos colaborativos e de longo prazo. O [[CSharp|C#]]oferece um sistema robusto de documentação XML que permite gerar documentação automática e auxiliar outros desenvolvedores a compreender rapidamente a intenção do código.

---
## 💡 **Por Que Comentar Código? (Com Exemplos)**

Comentários não são apenas anotações no código — eles são uma forma de **comunicação direta entre desenvolvedores**. Eles ajudam a explicar decisões importantes, lógicas complexas e intenções futuras. Veja alguns exemplos mais próximos da realidade de produção:

### ✅ **Exemplo Bom: Tratamento de Retentativas em Comunicação com API Externa**

```csharp
/// <summary>
/// Realiza a chamada à API de terceiros com retentativas em caso de falhas transitórias.
/// </summary>
/// <param name="endpoint">URL da API de destino.</param>
/// <param name="maxTentativas">Número máximo de tentativas antes de falhar.</param>
/// <returns>Resposta da API em formato JSON.</returns>
/// <exception cref="HttpRequestException">Lançada quando todas as tentativas falham.</exception>
public async Task<string> ChamarApiComRetentativaAsync(string endpoint, int maxTentativas = 3)
{
    int tentativa = 0;
    while (tentativa < maxTentativas)
    {
        try
        {
            var response = await httpClient.GetAsync(endpoint);
            response.EnsureSuccessStatusCode();
            return await response.Content.ReadAsStringAsync();
        }
        catch (HttpRequestException ex) when (tentativa < maxTentativas - 1)
        {
            // Falha transitória, tentar novamente.
            tentativa++;
            await Task.Delay(1000); // Aguardar 1 segundo antes da próxima tentativa.
        }
    }
    throw new HttpRequestException($"Falha ao acessar a API após {maxTentativas} tentativas.");
}
```

**Motivo**: O comentário explica claramente o comportamento esperado, parâmetros, exceções, e o contexto da retentativa. Isso evita que alguém remova a lógica de retentativa achando que é um retry desnecessário.

### 🚫 **Exemplo Ruim: Código Sem Comentário em Lógica Complexa de Produção**

```csharp
public async Task<string> ChamarApiAsync(string endpoint)
{
    for (int i = 0; i < 3; i++)
    {
        try
        {
            var response = await httpClient.GetAsync(endpoint);
            response.EnsureSuccessStatusCode();
            return await response.Content.ReadAsStringAsync();
        }
        catch { }
    }
    throw new Exception("Erro na chamada da API");
}
```

**Motivo**: Nenhuma explicação sobre o motivo das 3 tentativas, ausência de tratamento adequado de exceções e mensagens de erro genéricas. Esse tipo de código em produção causa dúvidas e comportamentos inesperados.

---
## ✅ **Boas Práticas Gerais**

- Seja claro, objetivo e evite redundâncias.
- Explique o _"porquê"_ do código, e não apenas o _"como"_.
- Utilize o sistema de comentários XML do C# para gerar documentação automática.
- Mantenha os comentários atualizados conforme o código evolui.
    
---
## 📚 **Comentários em Classes**

```csharp
/// <summary>
/// Classe responsável por gerenciar operações de pagamento.
/// </summary>
/// <remarks>
/// Esta classe centraliza a lógica de diferentes métodos de pagamento,
/// como cartão de crédito, boleto e PIX.
/// </remarks>
public class GerenciadorPagamento
{
    // Implementação da classe
}
```

### 📌 **Tags Utilizadas**

- `<summary>`: Descreve brevemente o propósito da classe.
- `<remarks>`: Fornece detalhes adicionais ou observações importantes.
    

---
## 📚 **Comentários em Atributos/Propriedades**

```csharp
/// <summary>
/// Identificador único do cliente.
/// </summary>
/// <value>Valor do tipo GUID representando o cliente.</value>
public Guid IdCliente { get; set; }
```

### 📌 **Tags Utilizadas**

- `<summary>`: Breve descrição da propriedade.
- `<value>`: Descreve o valor esperado ou retornado pela propriedade.
    
---
## 📚 **Comentários em Interfaces**

```csharp
/// <summary>
/// Interface que define operações básicas de repositórios genéricos.
/// </summary>
/// <typeparam name="T">Tipo da entidade manipulada pelo repositório.</typeparam>
public interface IRepositorio<T>
{
    /// <summary>
    /// Adiciona uma nova entidade ao repositório.
    /// </summary>
    /// <param name="entidade">Instância da entidade a ser adicionada.</param>
    void Adicionar(T entidade);
}
```

### 📌 **Tags Utilizadas**

- `<summary>`: Explica a finalidade da interface.
- `<typeparam>`: Descreve o tipo genérico utilizado.
- `<param>`: Explica os parâmetros dos métodos.
    
---
## 📚 **Comentários em Métodos**

```csharp
/// <summary>
/// Calcula o valor total de um pedido, aplicando descontos e impostos.
/// </summary>
/// <param name="valorBruto">Valor bruto do pedido.</param>
/// <param name="desconto">Percentual de desconto a ser aplicado.</param>
/// <param name="impostos">Lista de impostos a serem aplicados.</param>
/// <returns>
/// Valor final calculado após descontos e impostos.
/// </returns>
/// <exception cref="ArgumentOutOfRangeException">
/// Lançada quando o desconto é superior a 100%.
/// </exception>
public decimal CalcularTotal(decimal valorBruto, decimal desconto, List<decimal> impostos)
{
    if (desconto > 100) throw new ArgumentOutOfRangeException(nameof(desconto), "Desconto não pode ser maior que 100%.");

    decimal valorComDesconto = valorBruto - (valorBruto * (desconto / 100));
    foreach (var imposto in impostos)
    {
        valorComDesconto += valorComDesconto * (imposto / 100);
    }
    return valorComDesconto;
}
```

### 📌 **Tags Utilizadas**

- `<summary>`: Explica o objetivo do método.
- `<param>`: Detalha cada parâmetro de entrada.
- `<returns>`: Descreve o valor de retorno.
- `<exception>`: Informa possíveis exceções lançadas.

---
## 📚 **Outras Tags Úteis**

- `<example>`: Fornece exemplos de uso.
- `<see cref="">`: Faz referência a outros membros do código.
- `<seealso>`: Indica links adicionais para consulta.
- `<para>`: Cria um parágrafo dentro de outras tags.

```csharp
/// <example>
/// Exemplo de uso da classe:
/// <code>
/// var pagamento = new GerenciadorPagamento();
/// pagamento.Processar();
/// </code>
/// </example>
```

---
# 📌 **Conclusão**

Comentários claros e bem estruturados são um diferencial de código de qualidade. Utilize ao máximo as tags XML para facilitar a geração de documentação e tornar seu código mais acessível para qualquer desenvolvedor.

---
# 📚 **Tags**

#CSharp #Comentarios #Documentacao #CleanCode #BoasPraticas #DevTips #CodeQuality #DotNet #DevTips 