## 📖 **Introdução**

O *Single Responsibility Principle (SRP)* é o primeiro princípio do SOLID e estabelece que **uma classe deve ter apenas um motivo para mudar**. Ou seja, ela deve ser responsável por apenas uma funcionalidade ou comportamento específico dentro do sistema.

---
## 📖 **Por Que SRP é Importante?**

- **Facilita a Manutenção:** Mudanças impactam menos partes do sistema.
- **Melhora a Leitura e Entendimento:** Cada classe tem um papel claro.
- **Facilita Testes:** Classes pequenas e focadas são mais fáceis de testar.
- **Reduz Acoplamento:** Cada classe interage apenas com o que é necessário.

---
## ❌ **Exemplo de Violação do SRP**

```csharp
public class Relatorio
{
    public void GerarRelatorio() { /* Lógica de geração */ }
    public void ExportarParaPdf() { /* Exportação para PDF */ }
    public void EnviarPorEmail() { /* Envio por e-mail */ }
}
```

- A classe `Relatorio` está assumindo três responsabilidades:

    - Gerar o relatório.
    - Exportar o relatório.
    - Enviar o relatório por e-mail.

Se precisarmos alterar apenas a exportação, corremos o risco de impactar as outras funcionalidades.

---
## ✅ **Aplicando SRP Correto**

```csharp
public class Relatorio
{
    public string Conteudo { get; set; }
    public void Gerar() { /* Lógica de geração do conteúdo */ }
}

public class ExportadorPdf
{
    public void Exportar(Relatorio relatorio) { /* Lógica de exportação */ }
}

public class EmailService
{
    public void Enviar(string destinatario, string conteudo) { /* Lógica de envio de e-mail */ }
}
```

Cada classe agora tem uma única responsabilidade:

- `Relatorio` cuida apenas da geração do conteúdo.
- `ExportadorPdf` cuida apenas da exportação.
- `EmailService` cuida apenas do envio de e-mails.

---
## 🎯 **Quando Perceber Que Está Quebrando SRP?**

- A classe possui métodos que lidam com **lógicas de diferentes áreas**.
- Mudanças frequentes em partes isoladas da classe.
- Métodos longos e múltiplos comentários explicando blocos de código.

> **Dica**: Se você consegue dizer "Essa classe faz A e B", provavelmente está violando SRP.

---
## 📌 **Conclusão**

Aplicar o SRP resulta em um sistema mais **modular**, **fácil de manter** e **menos propenso a erros**. Embora pareça que mais classes estão sendo criadas, isso melhora a clareza e facilita a evolução do software.

---
# 📚 **Tags**

#CSharp #SOLID #SRP #CleanArchitecture #CleanCode #BoasPraticas #DevTips