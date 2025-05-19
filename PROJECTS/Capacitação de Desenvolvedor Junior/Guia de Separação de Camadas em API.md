## 📖 Introdução
Uma API bem estruturada segue o princípio da **Separação de Responsabilidades (SRP)**, um dos pilares do SOLID. Cada camada tem uma função clara, facilitando manutenção, testes e evolução do sistema.

---
## 📖 **Camadas Principais**

### 1️⃣ **Controllers (Apresentação)**

- **Responsabilidade**: Receber requisições HTTP e retornar respostas.
- **Não** deve conter lógica de negócio.
- **Deve** delegar para os Services.

#### ✅ Exemplo:

```csharp
[ApiController]
[Route("api/[controller]")]
public class ClientesController : ControllerBase
{
    private readonly IClienteService _clienteService;

    public ClientesController(IClienteService clienteService)
    {
        _clienteService = clienteService;
    }

    [HttpGet("{id}")]
    public IActionResult ObterCliente(int id)
    {
        var cliente = _clienteService.ObterPorId(id);
        return Ok(cliente);
    }
}
```

---
### 2️⃣ **Services (Regra de Negócio)**

- **Responsabilidade**: Implementar a lógica de negócio.
- **Não** deve acessar diretamente o banco de dados; usa Repositories.

#### ✅ Exemplo:

```csharp
public class ClienteService : IClienteService
{
    private readonly IClienteRepository _repository;

    public ClienteService(IClienteRepository repository)
    {
        _repository = repository;
    }

    public ClienteDto ObterPorId(int id)
    {
        var cliente = _repository.GetById(id);
        return new ClienteDto { Nome = cliente.Nome, Email = cliente.Email };
    }
}
```

---
### 3️⃣ **Repositories (Acesso a Dados)**

- **Responsabilidade**: Consultas e persistência de dados.
- **Deve** isolar o acesso ao banco, usando ORM como Entity Framework.

#### ✅ Exemplo:

```csharp
public class ClienteRepository : IClienteRepository
{
    private readonly AppDbContext _context;

    public ClienteRepository(AppDbContext context)
    {
        _context = context;
    }

    public Cliente GetById(int id)
    {
        return _context.Clientes.Find(id);
    }
}
```

---
### 4️⃣ **DTOs (Data Transfer Objects)**

- **Responsabilidade**: Definir modelos para transferência de dados entre camadas.
- Evita expor diretamente as entidades da base de dados.

#### ✅ Exemplo:

```csharp
public class ClienteDto
{
    public string Nome { get; set; }
    public string Email { get; set; }
}
```

---
### 5️⃣ **Entidades (Modelos de Domínio)**

- **Responsabilidade**: Representar a estrutura de dados no banco.
- Contém apenas propriedades e regras básicas de domínio.

#### ✅ Exemplo:

```csharp
public class Cliente
{
    public int Id { get; set; }
    public string Nome { get; set; }
    public string Email { get; set; }
}
```

---
## 📌 **Conclusão**

Separar corretamente as camadas traz inúmeros benefícios:

- Facilita a manutenção e leitura do código.
- Melhora a testabilidade.
- Evita dependências desnecessárias.
- Organiza o projeto de forma escalável.

---
# 📚 **Tags**

#CSharp #API #CleanArchitecture #SOLID #Camadas #REST #DesignPatterns #AspNet #DotNet
