## 📖 **Introdução**

A implementação de logs eficazes é fundamental para garantir a **manutenção**, **monitoramento** e **diagnóstico** de aplicações. O `ILogger` da Microsoft fornece uma maneira poderosa de registrar eventos, categorizá-los por níveis de importância e manter a rastreabilidade de forma estruturada.

---
## 📖 **Por Que Usar ILogger?**

- Centraliza a gravação de logs de forma padronizada.
- Permite controlar **o que registrar** e **onde armazenar**.
- Facilita a integração com sistemas de observabilidade (Azure Monitor, Elastic, Seq, Splunk).
- Ajuda no diagnóstico rápido de problemas, evitando longas investigações.
- Promove **logs estruturados** facilitando buscas e análise.

---
## 📚 **Níveis de Log e Quando Usar**

|Nível|Quando Usar|Exemplo|
|---|---|---|
|Trace|Informações extremamente detalhadas (para debugging profundo).|`_logger.LogTrace("Iniciando método com parâmetro {Parametro}", parametro);`|
|Debug|Informações úteis para desenvolvedores durante o desenvolvimento.|`_logger.LogDebug("Valor da variável X: {ValorX}", x);`|
|Information|Eventos importantes no fluxo normal da aplicação.|`_logger.LogInformation("Pedido {PedidoId} processado com sucesso.", pedidoId);`|
|Warning|Algo inesperado, mas que não impede a aplicação de continuar.|`_logger.LogWarning("Tentativa de login inválida para usuário {Usuario}", usuario);`|
|Error|Erros que afetam uma funcionalidade, mas a aplicação continua rodando.|`_logger.LogError(ex, "Erro ao processar pedido {PedidoId}", pedidoId);`|
|Critical|Falhas graves que podem derrubar a aplicação ou comprometer a integridade dos dados.|`_logger.LogCritical("Banco de dados indisponível! Aplicação será encerrada.");`|

---
## ✅ **Exemplo Completo de ILogger em um Controller**

```csharp
[ApiController]
[Route("api/[controller]")]
public class PedidoController : ControllerBase
{
    private readonly ILogger<PedidoController> _logger;

    public PedidoController(ILogger<PedidoController> logger)
    {
        _logger = logger;
    }

    [HttpPost]
    public IActionResult CriarPedido(PedidoDto pedido)
    {
        _logger.LogInformation("Iniciando criação de pedido para cliente {ClienteId}", pedido.ClienteId);

        try
        {
            if (pedido.Valor <= 0)
            {
                _logger.LogWarning("Pedido com valor inválido: {Valor}", pedido.Valor);
                return BadRequest("Valor do pedido inválido.");
            }

            // Simula um processamento
            _logger.LogDebug("Processando pedido: {@Pedido}", pedido);

            // Simula sucesso
            _logger.LogInformation("Pedido criado com sucesso para cliente {ClienteId}", pedido.ClienteId);

            return Ok();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Erro ao criar pedido para cliente {ClienteId}", pedido.ClienteId);
            return StatusCode(500, "Erro ao processar o pedido.");
        }
    }
}
```

> **Dica Importante:** Sempre utilize **placeholders** como `{Variavel}` no método de log. Isso permite logs estruturados, melhora a performance (evita formatação desnecessária) e facilita a consulta em ferramentas de monitoramento.

**Evite:**

```csharp
_logger.LogInformation($"Pedido criado para cliente {pedido.ClienteId}"); // NÃO recomendado
```

---
## 📌 **Melhores Práticas de Logging**

- Evite logar informações sensíveis (senhas, tokens, dados bancários).
- Sempre use placeholders para variáveis.
- Configure níveis de log diferentes por ambiente (ex: `Debug` em desenvolvimento, `Warning` em produção).
- Adicione logs nos **pontos críticos**: inícios de operações importantes, fluxos de exceção, resultados de integrações externas.
- Use `@` em `{}` para imprimir objetos completos de forma estruturada: `{@Objeto}`.

---
## 📚 **Importância dos Logs em Qualquer Linguagem de Programação**

Embora a forma de implementar logs varie entre as linguagens (como `ILogger` no C#, `Log4j` em Java, `winston` no Node.js ou `logging` no Python), a **importância de registrar eventos de forma clara e estruturada é universal**.

Um sistema sem logs adequados é como um carro sem painel de controle: você só percebe o problema quando é tarde demais. Independente da stack utilizada, implementar uma boa estratégia de logs é fundamental para manter a **observabilidade**, **diagnóstico rápido de problemas** e **segurança operacional**.

---
## 🎯 **Conclusão**

Um bom sistema de logs permite monitorar a aplicação em tempo real e agir rapidamente quando problemas acontecem. O uso adequado dos diferentes níveis de log ajuda a manter o foco nas informações mais relevantes, sem sobrecarregar os arquivos de log ou ferramentas de observabilidade.

---
# 📚 **Tags**

#CSharp #Logging #ILogger #Observabilidade #DevOps #BoasPraticas #Monitoramento #CleanCode #DotNet #Serilog