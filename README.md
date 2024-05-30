# Design Patterns
Este projeto visa demonstrar de maneira simples alguns exemplos de Design Patterns.
Existem 3 tipos definidos de design patterns como total de 23 padrões catalogados, o **Criacional**, **Estrutural** e **Comportamental**
![image](https://github.com/dccouto/Design-Patterns---Estruturais/assets/53017748/3de78253-7198-42ad-8116-ff3e092089ba)

# Estrutural

## Adapter
Esconde a real implementação da classe, disponibilizando apenas a interface para ser utilizada. Quando instanciar a classe, deverá passar o objeto que implementa o adapter. Assim ela não fica engessada com uma única implementação da interface.
Exemplo:

```
public interface AdaptadorInterface {
	void escreve(String texto);
}

public class UsandoPrintImpl implements AdaptadorInterface {

	@Override
	public void escreve(String texto) {
		System.out.print("Usando implementação de System.out.print \n");
		System.out.print(texto + "\n");
	}

}

public class UsandoPrintlnImpl implements AdaptadorInterface {

	@Override
	public void escreve(String texto) {
		System.out.println("Usando implementação de System.out.printLN");
		System.out.println(texto);
	}

}

public class ImprimirService {

	private AdaptadorInterface adaptadorInterface;
	
	public ImprimirService(AdaptadorInterface adaptadorInterface) {
		this.adaptadorInterface = adaptadorInterface;
	}

	public void imprime(String texto) {
		adaptadorInterface.escreve(texto);

	}
	
}

public class DesignPatternsAdapterApplication {

	public static void main(String[] args) {
		ImprimirService imprimirService = new ImprimirService(new UsandoPrintImpl());
		imprimirService.imprime("adapter 1");
		
		imprimirService = new ImprimirService(new UsandoPrintlnImpl());
		imprimirService.imprime("adapter 2");		
	}
}
```

## Decorator

Nesse design, uma classe compõe ela mesma.



```
package br.com.couto.decorators;

import java.math.BigDecimal;

public abstract class ImpostoDecorator {
	
	private ImpostoDecorator outro;
	
	public ImpostoDecorator(ImpostoDecorator imposto) {
		this.outro = imposto;
	}
	
	protected abstract BigDecimal realizaCalculo(BigDecimal orcamento);
	
	public BigDecimal calcular(BigDecimal orcamento) {
		BigDecimal valorDoImposto = realizaCalculo(orcamento);
		BigDecimal valorOutroImposto = BigDecimal.ZERO;
		
		if(outro != null) {
			valorOutroImposto = outro.realizaCalculo(orcamento);
		}
		return valorDoImposto.add(valorOutroImposto);
	} 

}

class ISS extends ImpostoDecorator{
	public ISS(ImpostoDecorator imposto) {
		super(imposto);
	}

	@Override
	protected BigDecimal realizaCalculo(BigDecimal orcamento) {
		return orcamento.multiply(new BigDecimal("0.1"));
	}
	
}

class ICMS extends ImpostoDecorator{
	public ICMS(ImpostoDecorator imposto) {
		super(imposto);
	}

	@Override
	protected BigDecimal realizaCalculo(BigDecimal orcamento) {
		return orcamento.multiply(new BigDecimal("0.15"));
	}
	
}



//Outro pacote
package br.com.couto.decorators;

import java.math.BigDecimal;


public class TestaImposto{

	public static void main(String[] args) {
		BigDecimal orcamento = BigDecimal.valueOf(100);
		//pode ir componte quando impostos tiver, e encerra com null quando não tem mais impostos
		System.out.println(new CalculadoraDeImpostos().calcular(orcamento, new ICMS(new ISS(null))));
	}
}

class CalculadoraDeImpostos {
	
	public BigDecimal calcular(BigDecimal orcamento, ImpostoDecorator imposto) {
		return imposto.calcular(orcamento);
	}
	
}

```

## Composite
Um objeto compõe o outro.
Nesse exemplo é adicionando orçamentos mais antigos ao orçamento mais atual, fazendo uma composição.
```
public class TestaComposicao {
	
	public static void main(String[] args) {
		Orcamento antigo = new Orcamento();
		antigo.adicionarItem(new ItemOrcamento("Notebook", BigDecimal.valueOf(5000.)));
		
		Orcamento maisAntigo = new Orcamento();
		maisAntigo.adicionarItem(new ItemOrcamento("Teclado", BigDecimal.valueOf(150.)));
		
		Orcamento novo = new Orcamento();
		novo.adicionarItem(new ItemOrcamento("Monitor", BigDecimal.valueOf(1000.)));
		
		novo.adicionarItem(antigo);
		novo.adicionarItem(maisAntigo);
		
		System.out.println(novo.getValor());
	}

}

class ItemOrcamento implements Orcavel{
	private String descricao;
	private BigDecimal valor;
	
	public ItemOrcamento(String descricao, BigDecimal valor) {
		this.descricao = descricao;
		this.valor = valor;
	}
	public String getDescricao() {
		return descricao;
	}

	@Override
	public BigDecimal getValor() {
		return valor;
	}
	
}


class Orcamento implements Orcavel{

	private BigDecimal valor;
	private List<Orcavel> itens;
	
	public Orcamento() {
		this.valor = BigDecimal.ZERO;
		this.itens =  new ArrayList<>();
	}

	@Override
	public BigDecimal getValor() {
		return valor;
	}

	public List<Orcavel> getOrcamento() {
		return itens;
	}
	
	public void adicionarItem(Orcavel item) {
		this.valor = valor.add(item.getValor());
		this.itens.add(item);
	}
	
}

interface Orcavel{
	BigDecimal getValor();
}
```

## Facade
O Facade define uma interface e esconde a implementação do classes (subsistemas) complexos, disponibilizando apenas um contrato para o cliente consumir.
Com isso, toda a complexidade do código fica escondida e sua utilização simplificada.
```
public class TestaFacade {

	public static void main(String[] args) {
		
		GeraPedido geraPedido = new GeraPedido("Compra de Notebook");
		
		RealizaVendaFacade vendaFacade = new RealizaVendaFacade(
				List.of(new Imprime(), new Estoque(), new RegistraLog()));
		
		//O executar gera várias tarefas, porém é escondido no encapsulamento da classe vendaFacade
		vendaFacade.executar(geraPedido);

	}
}

class GeraPedido{
	private String pedido;

	public GeraPedido(String pedido) {
		this.pedido = pedido;
	}
	
	public String getPedido() {
		return pedido;
	}
}

class RealizaVendaFacade {
	private List<ExecutaAcao> acoesAposVenda;

	//injeção de dependência
	public RealizaVendaFacade(List<ExecutaAcao> acoesAposVenda) {
		this.acoesAposVenda = acoesAposVenda;
	}
	
	public void executar(GeraPedido geraPedido) {
		this.acoesAposVenda.forEach(acao -> acao.executar(geraPedido.getPedido()));
	}
	
}

interface ExecutaAcao{
	void executar(String texto);
}

class Imprime implements ExecutaAcao{
	
	@Override
	public void executar(String texto) {
		System.out.println("Executando a impressão do pedido: " + texto);
	}
}

class RegistraLog implements ExecutaAcao{

	@Override
	public void executar(String texto) {
		System.out.println("Executando o registro de log do pedido: " + texto);
	}
}

class Estoque implements ExecutaAcao{

	@Override
	public void executar(String texto) {
		System.out.println("Executando a baixa de estoque do pedido: " + texto);
	}
}
```

## Proxy
O Proxy soluciona o proplema de quando uma requisição ou serviço externo é muito lento e não temos acesso a API de terceiros.
Caso precisamos utilizar o mesmo valor mais vezes durante a execução, podemos criar o proxy para guardar o valor que foi acessado,
da primeira vez, assim aumentamos a performace do sistema.
```
public class TestaProxy {

	public static void main(String[] args) {

		Orcamento orcamento = new Orcamento(new BigDecimal(5000));
		//Sem utilizar o proxy, fica muito lento
		//System.out.println(orcamento.getValor());
		//System.out.println(orcamento.getValor());
		//System.out.println(orcamento.getValor());
		
		//Com proxy, apenas a primeira requisição é lenta as demais estão em "cache"
		OrcamentoProxy proxy = new OrcamentoProxy(orcamento);
		System.out.println(proxy.getValor());
		System.out.println(proxy.getValor());
		System.out.println(proxy.getValor());
		System.out.println(proxy.getValor());
	}

}

class Orcamento {
	private BigDecimal valor;
	
	protected Orcamento() {}
	public Orcamento(BigDecimal valor) {
		this.valor = valor;
	}

	public BigDecimal getValor() {
		try {
			//simulando uma requisição lenta para algum serviço
			Thread.sleep(3000);
		} catch (InterruptedException e) {
			throw new RuntimeException(e);
		}
		return valor;
	}
}

class OrcamentoProxy extends Orcamento{
	private BigDecimal valor;
	private Orcamento orcamento;
	
	public OrcamentoProxy(Orcamento orcamento) {
		this.orcamento = orcamento;
	}
	
	@Override
	public BigDecimal getValor() {
		//realizando o proxy (cache)
		if(this.valor == null) {
			this.valor = this.orcamento.getValor();
		}
		return valor;
	}
}
```
## Singleton
O Singleton garante uma única instancia de um objeto para a aplicação

```
/**
* @see <a href="https://stackoverflow.com/a/24018148">
*
*/
public class SingletonLazyHolder {

	private static class InstanceHolder {
		public static SingletonLazyHolder instancia = new SingletonLazyHolder();
	}
	private SingletonLazyHolder() {
	}

	public static SingletonLazyHolder getInstance() {
		return InstanceHolder.instancia;
	}
}
```

## Repository
Faz a abstração ("meio de campo") entre o domínio da aplicação e a camada de dados.

