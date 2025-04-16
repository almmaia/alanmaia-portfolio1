# alanmaia-portfolio1
namespace RO.DevTest.Domain.Entities;

public class Cliente
{
    public Guid Id { get; set; }
    public string Nome { get; set; } = null!;
    public string Email { get; set; } = null!;
    public string Telefone { get; set; } = null!;
    public DateTime DataCadastro { get; set; } = DateTime.UtcNow;
}

using RO.DevTest.Domain.Entities;

namespace RO.DevTest.Domain.Repositories;

public interface IClienteRepository
{
    Task AddAsync(Cliente cliente);
    // Outros métodos como GetByIdAsync, UpdateAsync, DeleteAsync, etc.
}
public record CreateClienteCommand(string Nome, string Email, string Telefone) : IRequest<Guid>;
public class CreateClienteCommandHandler : IRequestHandler<CreateClienteCommand, Guid>
{
    private readonly IClienteRepository _repository;

    public CreateClienteCommandHandler(IClienteRepository repository)
    {
        _repository = repository;
    }

    public async Task<Guid> Handle(CreateClienteCommand request, CancellationToken cancellationToken)
    {
        var cliente = new Cliente
        {
            Id = Guid.NewGuid(),
            Nome = request.Nome,
            Email = request.Email,
            Telefone = request.Telefone
        };

        await _repository.AddAsync(cliente);
        return cliente.Id;
    }
}[ApiController]
[Route("api/[controller]")]
public class ClientesController : ControllerBase
{
    private readonly IMediator _mediator;

    public ClientesController(IMediator mediator)
    {
        _mediator = mediator;
    }

    [HttpPost]
    public async Task<IActionResult> Post([FromBody] CreateClienteCommand command)
    {
        var id = await _mediator.Send(command);
        return CreatedAtAction(nameof(Post), new { id }, command);
    }
}


