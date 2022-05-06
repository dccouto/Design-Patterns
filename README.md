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

## Composite

## Facade

## Proxy
