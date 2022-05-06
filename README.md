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

## Facade

## Proxy
