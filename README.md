# Guia de Estilo para Objective-C na Infoglobo

Este guia de estilo define as convenções de código da equipa de Aplicativos Móveis na Infoglobo. O seu feedback é bem vindo.

## Introdução

Aqui estão alguns documentos da Apple que contribuiram para a formação do guia de estilo. Se algo não é mencionado aqui, provavelmente é abrangido em detalho num dos seguintes:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Conteúdo

* [Sintaxe de Dot-Notation](#dot-notation-syntax)
* [Espaçamento](#spacing)
* [Condicionais](#conditionals)
  * [Operador ternário](#ternary-operator)
* [Golden Path](#golden-path)
* [Processamento de Erros](#error-handling)
* [Métodos](#methods)
* [Variáveis](#variables)
* [Nomenclatura](#naming)
  * [Underscores](#underscores)
* [Comentários](#comments)
* [Init & Dealloc](#init-and-dealloc)
* [Literais](#literals)
* [Funções CGRect](#cgrect-functions)
* [Constantes](#constants)
* [Tipos Enumerados](#enumerated-types)
* [Propriedades Privadas](#private-properties)
* [Nomenclatura de Imagens](#image-naming)
* [Boleanos](#booleans)
* [Singletons](#singletons)
* [Projecto Xcode](#xcode-project)
* [Escuteiro](#boyscout)

## Sintaxe de Dot-Notation

Dot-notation deverá ser **sempre** utilizado para aceder a e alterar propriedades. Notação de chaveta é preferida em todos os outros casos.

**Por exemplo:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Não:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Espaçamento

* Indentar com 4 espaços. Nunca com tabs. Certifique-se de que o Xcode está devidamente configurado.
* Chavetas de método, assim como outras chavetas (`if`/`else`/`switch`/`while` etc.) abrem sempre na mesma linha do statement mas fecham numa linha nova.

**Exemplo:**
```objc
if (user.isHappy) {
  // Fazer alguma coisa
} else {
  // Fazer outra coisa
}
```
* Deverá existir exactamente uma linha em branco entre métodos para ajudar na claridade visual e organização. Espaço em branco dentro de métodos deverá separar funcionalidade, mas frequentemente isso é um indicador de que o código deverá residir noutro método.
* `@synthesize` e `@dynamic` devem ser declarados em linhas novas na implementation.

## Condicionais

Condicionais deverão utilizar sempre chavetas, mesmo quando poderia ser escrita sem chavetas (e.g., apenas uma linha) para prevenir erros. Esse erros incluem adicionar uma segunda linha com a expectativa de que fará parte do if. Outro erro, [ainda mais perigoso](http://programmers.stackexchange.com/a/16530) pode acontecer em que a linha "dentro" do if é comentada, e a linha seguinte passa a fazer parte do if. Adicionalmente, este estilo é mais consistente e mais fácil de ler rapidamente.

**Exemplo:**
```objc
if (!error) {
    return success;
}
```

**Não:**
```objc
if (!error)
    return success;
```

ou

```objc
if (!error) return success;
```

### Operador ternário

O operador ternário, ? :, deverá apenas ser utilizado quando aumenta a claridade ou a limpeza de código. Uma condição, normalmente, é tudo o que deve ser calculado. Calcular mais do que uma condição é normalmente mais fácil de ler quando escrito sob a forma de um if, ou refactorado para variáveis de instância. 

**Exemplo:**
```objc
result = a > b ? x : y;
```

**Não:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Golden Path
Ao escrever código condiconal, a marge esquerda do código deve ser o "golden path", ou caminho "feliz".

**Não:**
```objc
- (void)someMethod
{
  if ([someOther boolValue]) {
    // Fazer algo importante
  }
}
```

Isso é difícil de ler. No lugar, escreva código que falha cedo e falha frequentemente:

**Exemplo:**
```objc
- (void)someMethod
{
  if (![someOther boolValue]) return;
  // Fazer algo importante
}
```

Um método não deverá ser dividido a meio com uma condição.

**Não:**
```objc
- (void)someMethod
{
  if ([someOther boolValue]) {
    // Fazer algo importante
  } else {
    // Fazer outra coisa importante
  }
}
```

Isso é difícil de ler de deverá ser reescrito como:

**Exemplo:**
```objc
- (void)someMethod
{
  if ([someOther boolValue]) {
    //  Fazer algo importante
    return;
  }
  // Fazer outra coisa importante
}
```

Eis uma situação em que uma bisecção é aceitável:

**Exemplo:**
```objc
- (void)someMethod
{
  if ([someOther boolValue]) {
    // Fazer algo importante
  } else {
    // Fazer outra coisa importante
  }
  // Fazer isto de qualquer forma
}
```

O objectivo é fazer com que o código na margem esquerda seja o código "esperado", e que o código indentado seja a excepção. Consistência nesta área é importante para a leitura do código.

## Processamento de Erros

Quando um método retorna um parâmetro de erro por referência, faça switch no valor retornado, não na variável de erro.

**Exemplo:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Lidar com erro
}
```

**Não:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Lidar com erro
}
```

Alguns dos API's da Apple escrevem lixo no parâmetro de erro (caso não seja nulo) em casos de sucesso, por isso utilizar um switch no erro pode causar falsos negativso (e subsequentes crashes).

## Métodos

Nas assinaturas de método, deverá existir um espaço depois do escopo (símbolo -/+). Deverá existir um espaço entre os segmentos do método.

**Exemplo:**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```
## Variáveis

As variávis deverão ser nomeadas da forma mais descritiva possível. Variávis de uma só letra deverão ser evitadas, excepto em ciclos `for()`.

Asteriscos que indicam pointers pertencem à variável, e.g., `NSString *text` não `NSString* text` ou `NSString * text`, excepto no caso de constantes.

Definições de propriedades deverão ser utilizados em vez de variáveis de instância sempre que possível. Acesso directo a variáveis de instância deverá ser evitado, excepto em métodos de inicialização (`init`, `initWithCoder:`, etc…), métodos `dealloc` e em setters and getters escritos à mão. Para mais informações sobre métodos de acesso em inicializadores e dealloc, veja [aqui](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**Exemplo:**

```objc
@interface IGBSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**Não:**

```objc
@interface IGBSection : NSObject {
    NSString *headline;
}
```

## Nomenclatura

As convenções de nomenclatura da Apple devem ser seguidas sempre que possível, especialmente as relacionadas com [regras de gestão de memória](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Nomes longos e descritivos são bons.

**Exemplo:**

```objc
UIButton *settingsButton;
```

**Não:**

```objc
UIButton *setBut;
```

Um prefixo de três letras (e.g. `IGB`) deverá ser sempre utilizado para nomes de classes e constantes, no entanto poderão ser omitidos no caso de nomes de entidade no Core Data. Constantes devem ser em camel-case com todas as palavras com inicial maiúscula, e prefixadas pela classe relacionada para efeitos de clareza.

**Exemplo:**

```objc
static const NSTimeInterval IGBArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Não:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

Propriedades deverão ser escritas em camel-case com a palavra inicial em minúsculas. **Se o Xcode puder sintetizar automaticamente a variável, então deixe-.** Caso contrário, de forma a ser consistente, as variáveis de instância associadas deverão ser escritas em camel-case com a palavra inicial em minúsculas, e um underscore de prefixo. Este é o mesmo formato utilizado pela síntese automática do Xcode.

**Exemplo:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Não:**

```objc
id varnm;
```

### Underscores

Quando a utilizar propriedades, variáveis de instância deverão sempre ser acedidas e alteradas utilizando `self.`. Isso quer dizer que todas as propriedades deverão ser visualmente distinctas, dado que todas serão precedidas de `self.`. Variáveis locais não deverão conter underscores.

## Comentários

Quando necessários, comentários deverão ser utilizados para explicar **por que** um bocado de código faz o que faz. Os comentários deverão ser mantidos actualizados ou eliminados.

Comentários de bloco deverão ser evitados, dado que o código deve-se auto-documentar o mais possível, com apenas uma necessidade irregular de explicações de poucas linhas. Isto não se aplica a comentários utilizados para gerar documentação.

## init e dealloc

Métodos `dealloc` deverão ser colocados no início da implementation, imediatamente a seguir aos `@synthesize` e `@dynamic`. `init` deverá ser colocado imediatamente a seguir aos métodos `dealloc` em todas as classes.

Métodos `init` deverão ser estruturados da seguinte forma:

```objc
- (instancetype)init {
    self = [super init]; // Ou chamar inicializador designado
    if (!self) {
        return self;
    }

    // Inicialização customizada
    return self;
}
```

## Literais

Literais `NSString`, `NSDictionary`, `NSArray`, e `NSNumber` deverão ser utilizados aquando da criação de instâncias imutáveis desses objectos. Preste atenção para que valores `nil` não sejam passados para literais `NSArray` e `NSDictionary`, já que isso causa um crash.

**Exemplo:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Não:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## Funções CGRect

Ao aceder a `x`, `y`, `width`, ou `height` de um `CGRect`, utilize sempre as [funções de `CGGeometry`](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) no lugar de acesso directo aos membros da struct.

**Exemplo:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Não:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Constantes

Prefira constantes em vez de literais in-line, ou números mágicos, dado que isso permite reproduzir variáveis utilizadas frequentemente, e podem ser alteradas sem find e replace. As constantes deverão ser declaradas como `static` e não como `#define`s, excepto no caso em que realmente são parte de uma macro.

**Exemplo:**

```objc
static NSString * const IGBAboutViewControllerCompanyName = @"Infoglobo";

static const CGFloat IGBImageThumbnailHeight = 50.0;
```

**Não:**

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## Tipos Enumerados

Ao utiliza `enum`s, é recomendado que utilize a nova especificação com tipo subjacente porque permite verificação de tipo e o Xcode consegue completar código automaticamente. O SDK inclui uma macro que facilita e encoraja a utlização de tipos subjacentes — `NS_ENUM()`

**Exemplo:**

```objc
typedef NS_ENUM(NSInteger, IGBAdRequestState) {
    IGBAdRequestStateInactive,
    IGBAdRequestStateLoading
};
```

## Propriedades Privadas

Propriedades privadas deverão ser declaraas em extensões de classe (categorias anónimas) no ficheiro de implementação da classe. Categorias com nome (como `IGBPrivate` ou `private`) nunca deverão ser utilizadas, a menos que estejam realmenet a estender outra classe.

**Exemplo:**

```objc
@interface IGBAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## Nomenclatura de Imagens

Nomes de imagens deverão ser consistentes para preservar organização e a sanidade dos desenvolvedores. Deverão ser nomeadas com camel case do seu propósito, seguido da classe sem prefixo, ou propriedade que estão customizando, seguido de uma descrição de côr ou colocação, e finalmente o seu estado.

**Exemplo:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Imagens que são utilizadas para um propósito semelhante deverão ser agrupadas em assets de xcode.

## Boleanos

Considerando que `nil` é equivalente a `NO`, não é necessário compará-los numa condição. Nunca compare nada a  `YES`, porque `YES` está definido como 1 e um `BOOL` pode ter até 8 bits.

Isto permite mais consistência e mais claridade visual.

**Exemplo:**

```objc
if (!someObject) {
}
```

**Não:**

```objc
if (someObject == nil) {
}
```

-----

**Para `BOOL`, aqui estão dois exemplos:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**Não:**

```objc
if ([someObject boolValue] == NO)
if (isAwesome == YES) // Nunca fazer isto.
```

-----

Se o nome de uma propriedade `BOOL` é exprimida como adjectivo, a propriedade pode omitir o prefixo “is”, mas especifica o prefixo convencional para o getter. Por exemplo:

```objc
@property (assign, getter=isEditable) BOOL editable;
```

## Singletons

Objectos singleton deverão utilizar um padrão thread-safe para criar a instância partilhada.

```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
Isso previnirá [crashes possíveis](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Projecto Xcode

Os ficheiros físicos deversão ser mantidos em sincronia com o projecto Xcode, para evitar a desorganização. Grupos de Xcode deverão estar associados a directórios no sistema de ficheiros. Código deverá ser agrupado não só por tipo, como por funcionalidade.

Sempre que possível, active o "Treat Warnings as Errors" nos settings de build, e active o máximo possível de [avisos opcionais](http://boredzo.org/blog/archives/2009-11-07/warnings). Se precisar de ignorar um aviso específico, utilize um [pragma de Clang](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

## Escuteiro

Sempre que estiver a fazer alterações no código, deixe-o mais limpo do que o encontrou. Se encontrar código que viole este guia, corrija-o. Se o código é antigo, actualize-o.
