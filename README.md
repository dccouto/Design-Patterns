# Design-Patterns-Estruturais
Este projeto visa demonstrar de maneira simples alguns exemplos de Design Patterns estruturais.

## Adapter
ESconde a real implementação da classe, disponibilizando apenas a interface para ser utilizada. Quando instanciar a classe, deverá passar o objeto que implementa o adapter. Assim ela não fica engessada com uma única implementação da interface.
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

## Proxy
