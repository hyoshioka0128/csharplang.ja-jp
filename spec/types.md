---
ms.openlocfilehash: a28397b1ce97dbead6d5014e2b20e108a1018502
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229777"
---
# <a name="types"></a>種類

C# 言語の型は、2 つの主なカテゴリに分類されます。***値の型***と***参照型***します。 値型と参照型の両方があります***ジェネリック型***、これは、1 つ以上時間がかかる***パラメーター入力***します。 型パラメーターでは、両方の値の型を指定でき、型を参照することができます。

```antlr
type
    : value_type
    | reference_type
    | type_parameter
    | type_unsafe
    ;
```

型、ポインターの最終的なカテゴリは、アンセーフ コードでのみ使用できます。 これについては説明でさらに[ポインター型](unsafe-code.md#pointer-types)します。

値の型とは異なりは参照型で値型の変数は、データを直接格納変数参照の種類のストアは***参照***として認識されている後者、データに***オブジェクト***. 参照型では 2 つの変数が同じオブジェクトを参照できるため、他の変数によって参照されるオブジェクトに影響を与える 1 つの変数に対する操作。 値型の場合は、各変数が独自のデータのコピーにあり、操作に影響を与えるもう 1 つのことはできません。

# の型システムは、任意の型の値をオブジェクトとして処理できるように統合されます。 C# における型はすべて、直接的または間接的に `object` クラス型から派生し、`object` はすべての型の究極の基底クラスです。 参照型の値は、値を単純に `object` 型としてみなすことによってオブジェクトとして扱われます。 値型の値がボックス化とボックス化解除操作を実行することによってオブジェクトとして扱われます ([ボックス化とボックス化解除](types.md#boxing-and-unboxing))。

## <a name="value-types"></a>値型

値型は、構造体の型または列挙型のいずれかです。 C# と呼ばれる定義済みの構造体の型のセットを提供する、***単純型***します。 単純型は、予約語によって特定されます。

```antlr
value_type
    : struct_type
    | enum_type
    ;

struct_type
    : type_name
    | simple_type
    | nullable_type
    ;

simple_type
    : numeric_type
    | 'bool'
    ;

numeric_type
    : integral_type
    | floating_point_type
    | 'decimal'
    ;

integral_type
    : 'sbyte'
    | 'byte'
    | 'short'
    | 'ushort'
    | 'int'
    | 'uint'
    | 'long'
    | 'ulong'
    | 'char'
    ;

floating_point_type
    : 'float'
    | 'double'
    ;

nullable_type
    : non_nullable_value_type '?'
    ;

non_nullable_value_type
    : type
    ;

enum_type
    : type_name
    ;
```

参照型の変数とは異なり、値型の変数には、値も含めることができます`null`値の型が null 許容型である場合にのみです。  すべての null 非許容値型の値と値の同じセットを示す対応する null 許容値型は`null`します。

値型の変数への代入では、割り当てられている値のコピーを作成します。 これは、コピー、参照が参照によって識別されるオブジェクトではなく、参照型の変数に割り当てと異なります。

### <a name="the-systemvaluetype-type"></a>System.ValueType 型

すべての値型がクラスから暗黙的に継承`System.ValueType`であり、さらに、クラスを継承`object`します。 任意の種類、値型から派生することはできませんし、値の型が暗黙的にシールしたがって ([クラスをシール](classes.md#sealed-classes))。

なお`System.ValueType`自体ではありません、 *value_type*します。 *Class_type*すべてから*value_type*s が自動的に派生します。

### <a name="default-constructors"></a>既定のコンストラクター

すべての値型が暗黙的に呼び出されるパラメーターなしのパブリック インスタンス コンス トラクターを宣言、***既定のコンス トラクター***します。 既定のコンス トラクターと呼ばれる、ゼロ初期化のインスタンスを返します、***既定値***値の型。

*  すべての*simple_type*s、既定値はすべてゼロのビット パターンで生成される値。
    * `sbyte`、 `byte`、 `short`、 `ushort`、 `int`、 `uint`、 `long`、および`ulong`、既定値は`0`します。
    * `char`、既定値は`'\x0000'`します。
    * `float`、既定値は`0.0f`します。
    * `double`、既定値は`0.0d`します。
    * `decimal`、既定値は`0.0m`します。
    * `bool`、既定値は`false`します。
*  *Enum_type* `E`、既定値は`0`型に変換された`E`します。
*  *Struct_type*、既定値は、すべての値型フィールドが既定値をすべて参照型フィールドに設定された値`null`します。
*  *Nullable_type*既定値は、対象のインスタンス、`HasValue`プロパティが false と`Value`プロパティが定義されていません。 既定値とも呼ばれますが、 ***null 値***null 許容型のです。

使用して値型の既定のコンス トラクターを呼び出すその他のインスタンス コンスのように、`new`演算子。 効率性の理由から、この要件はコンス トラクターの呼び出しを生成する実装が実際にあるありません。 変数の下の例では`i`と`j`両方は 0 に初期化します。

```csharp
class A
{
    void F() {
        int i = 0;
        int j = new int();
    }
}
```

すべての値型に暗黙的には、パラメーターなしのパブリック インスタンス コンス トラクターがあるために、パラメーターなしのコンス トラクターの明示的な宣言を格納する構造体の型のことはできません。 パラメーター化されたインスタンス コンス トラクターを宣言する構造体の型が許可されているただし ([コンス トラクター](structs.md#constructors))。

### <a name="struct-types"></a>構造体の型

構造体の型とは、定数、フィールド、メソッド、プロパティ、インデクサー、演算子、インスタンス コンス トラクター、静的コンス トラクター、および入れ子にされた型を宣言する値型です。 構造体型の宣言については、「[構造体の宣言](structs.md#struct-declarations)します。

### <a name="simple-types"></a>単純型

C# と呼ばれる定義済みの構造体の型のセットを提供する、***単純型***します。 予約語を使って単純型が識別されますが、これらの予約語は定義済みの構造体の型に対する単なるエイリアス、`System`名前空間では、次の表で説明されているとします。


| __予約語__ | __エイリアスの型__ |
|-------------------|------------------|
| `sbyte`           | `System.SByte`   | 
| `byte`            | `System.Byte`    | 
| `short`           | `System.Int16`   | 
| `ushort`          | `System.UInt16`  | 
| `int`             | `System.Int32`   | 
| `uint`            | `System.UInt32`  | 
| `long`            | `System.Int64`   | 
| `ulong`           | `System.UInt64`  | 
| `char`            | `System.Char`    | 
| `float`           | `System.Single`  | 
| `double`          | `System.Double`  | 
| `bool`            | `System.Boolean` | 
| `decimal`         | `System.Decimal` | 

単純型のエイリアスは、構造体型、ため、すべての単純型はメンバーがあります。 たとえば、`int`で宣言されたメンバーが含まれて`System.Int32`から継承したメンバーと`System.Object`と、次のステートメントが許可されます。

```csharp
int i = int.MaxValue;           // System.Int32.MaxValue constant
string s = i.ToString();        // System.Int32.ToString() instance method
string t = 123.ToString();      // System.Int32.ToString() instance method
```

単純型は、ある追加の操作を許可している点で、他の構造体型とは異なります。

*  ほとんどの単純型を記述して作成する値を許可する*リテラル*([リテラル](lexical-structure.md#literals))。 たとえば、`123`型のリテラルは、`int`と`'a'`型のリテラルは、`char`します。 C# の場合は、構造体の型のリテラルをプロビジョニングする一般に、他の構造体の型の既定以外の値が常にこれらの構造体の型のインスタンス コンス トラクターを通じて作成最終的に。
*  式のオペランドがすべて単純型の定数の場合は、コンパイラはコンパイル時に式の評価になります。 このような式と呼ばれる、 *constant_expression* ([定数式](expressions.md#constant-expressions))。 その他の構造体の型で定義された演算子を含む式は定数式と見なされません。
*  を通じて`const`単純型の定数を宣言することは、宣言 ([定数](classes.md#constants))。 その他の構造体の型の定数を持つことはありませんが、同様の効果がによって提供される`static readonly`フィールド。
*  単純型を使用する変換は、他の構造体型で定義された変換演算子の評価に参加できますが、ユーザー定義変換演算子がもう 1 つのユーザー定義演算子の評価に参加できることはありません ([の評価ユーザー定義の変換](conversions.md#evaluation-of-user-defined-conversions))。

### <a name="integral-types"></a>整数型

C# では、9 つの整数型: `sbyte`、 `byte`、 `short`、 `ushort`、 `int`、 `uint`、 `long`、 `ulong`、および`char`します。 整数型では、次のサイズと値の範囲があります。

*  `sbyte`符号付き 8 ビット整数-128 から 127 まで値の型を表します。
*  `byte`型が符号なし 8 ビット整数 0 ~ 255 の範囲の値を表します。
*  `short`署名された-32768 から 32767 の値を 16 ビット整数の型を表します。
*  `ushort`型が符号なし 16 ビット整数 0 ~ 65535 の値を表します。
*  `int`符号付き 32 ビット整数-2147483648 から 2147483647 までの値の型を表します。
*  `uint`型は、0 ~ 4294967295 の値の符号なし 32 ビット整数を表します。
*  `long`符号付き 64 ビット整数-9223372036854775808 から 9223372036854775807 まで値の型を表します。
*  `ulong`型が符号なし 64 ビット整数 0 から 18446744073709551615 までの値を表します。
*  `char`型が符号なし 16 ビット整数 0 ~ 65535 の値を表します。 使用できる値のセット、`char`種類は、Unicode 文字セットに対応します。 `char`と同じ表現を持つ`ushort`、他の 1 つの型で許可されているすべての操作が許可されています。

整数型の単項および二項演算子は、常に符号付き 32 ビットの有効桁数、符号なし 32 ビットの有効桁数、符号付き 64 ビットの有効桁数、または符号なし 64 ビットの精度の動作します。

*  単項`+`と`~`演算子、オペランドを型に変換されます`T`ここで、`T`の最初の`int`、 `uint`、 `long`、および`ulong`すべてを完全にはオペランドの値。 操作が型の有効桁数を使用して実行し、 `T`、および結果の型は`T`します。
*  単項`-`演算子、オペランドを型に変換されます`T`ここで、`T`の最初の`int`と`long`オペランドのすべての値を表す完全ことができます。 操作が型の有効桁数を使用して実行し、 `T`、および結果の型は`T`します。 単項`-`演算子は、型のオペランドに適用することはできません`ulong`します。
*  バイナリの`+`、 `-`、 `*`、 `/`、 `%`、 `&`、 `^`、 `|`、 `==`、 `!=`、 `>`、 `<`、 `>=`、および`<=`演算子、オペランドを型に変換されます`T`ここで、`T`の最初の`int`、 `uint`、 `long`、および`ulong`すべてを表す完全両方のオペランドの値。 操作が型の有効桁数を使用して実行し、 `T`、および結果の型は`T`(または`bool`関係演算子)。 1 つのオペランドの型は許可されていません`long`およびその他の型`ulong`二項演算子でします。
*  バイナリの`<<`と`>>`演算子、左のオペランドを型に変換されます`T`ここで、`T`の最初の`int`、 `uint`、 `long`、および`ulong`すべてを完全にはオペランドの値。 操作が型の有効桁数を使用して実行し、 `T`、および結果の型は`T`します。

`char`型が整数型に分類されますが、2 つの方法で他の整数型とは異なります。

*  他の型から暗黙的な変換がない、`char`型。 具体的には、場合でも、 `sbyte`、 `byte`、および`ushort`型を使用して完全に表現できる値の範囲がある、`char`から暗黙的な変換を入力します`sbyte`、 `byte`、または`ushort`に。`char`はありません。
*  定数、`char`として型を記述する必要があります*character_literal*s または*integer_literal*型へのキャストと組み合わせて s`char`します。 たとえば、`(char)10` は `'\x000A'` と同じです。

`checked`と`unchecked`演算子とステートメントは、整数型の算術演算および変換に対するオーバーフロー チェックの制御に使用されます ([checked と unchecked 演算子](expressions.md#the-checked-and-unchecked-operators))。 `checked`コンテキスト、オーバーフローがコンパイル時エラーを生成またはにより、`System.OverflowException`がスローされます。 `unchecked`コンテキストはオーバーフローは無視され、変換先の型に適合しない任意の上位ビットは破棄されます。

### <a name="floating-point-types"></a>浮動小数点型

C# には、2 つがサポートしている浮動小数点型:`float`と`double`します。 `float`と`double`形式を使用して、32 ビット単精度と 64 ビット倍精度 IEEE 754、次の値のセットを提供する型が表されます。

*  正および負のゼロ。 ほとんどの場合、正のゼロと負のゼロ動作は同じように、単純な値のゼロが区別して 2 つの特定の操作 ([除算演算子](expressions.md#division-operator))。
*  正の無限大と負の無限大。 無限大は、0 以外の数値をゼロ除算などの操作によって生成されます。 たとえば、`1.0 / 0.0`正の無限大と`-1.0 / 0.0`と負の無限大。
*  ***Not 非数***値、NaN の多くの場合省略されます。 Nan は、0 を 0 除算などの無効な浮動小数点演算によって生成されます。
*  フォームの値が 0 以外の有限のセット`s * m * 2^e`ここで、 `s` 1 または-1 の場合と`m`と`e`は、特定の浮動小数点型によって決まります。`float`、`0 < m < 2^24`と`-149 <= e <= 104`、および`double`、`0 < m < 2^53`と`1075 <= e <= 970`します。 非正規化浮動小数点数は、有効な値が 0 以外と見なされます。

`float`型が約範囲の値を表す`1.5 * 10^-45`に`3.4 * 10^38`7 桁の有効桁数を持つ。

`double`型が約範囲の値を表す`5.0 * 10^-324`に`1.7 × 10^308`15 ~ 16 桁の有効桁数を持つ。

二項演算子のオペランドの 1 つの浮動小数点型の場合、し、もう一方のオペランドが整数型または浮動小数点型のある必要があり、操作は次のように評価されます。

*  整数型のオペランドのいずれかの場合は、そのオペランドがもう一方のオペランドの浮動小数点型に変換されます。
*  型のオペランドのいずれかの場合は、 `double`、もう一方のオペランドに変換`double`、少なくともを使用して、操作を実行`double`範囲と有効桁数、および結果の型は`double`(または`bool`の関係演算子)。
*  それ以外の場合、操作を使用して実行少なくとも`float`範囲と有効桁数、および結果の型は`float`(または`bool`関係演算子)。

浮動小数点の演算子、代入演算子を含む例外が発生することはありません。 代わりに、例外的な状況は、浮動小数点演算が生成 0、無限大、または NaN の場合、以下に示すよう。

*  浮動小数点演算の結果が変換先の形式に対して小さすぎる場合は、操作の結果は正の値 0 になるか、負のゼロ。
*  浮動小数点演算の結果が大きすぎて変換先の形式の場合、操作の結果は正の無限大または負の無限大になります。
*  浮動小数点演算が有効でない場合、操作の結果は NaN になります。
*  浮動小数点演算のオペランドの一方または両方が NaN の場合は、操作の結果は NaN になります。

浮動小数点演算は、操作の結果の型よりも高い精度で実行できます。 一部のハードウェア アーキテクチャでの大きい範囲とより有効桁数を持つ「拡張」または"long double"浮動小数点型のサポートなど、 `double` 「」暗黙的にこのより高い有効桁数型を使用してすべての浮動小数点演算を実行します。 過剰なパフォーマンス コストでのみこのようなハードウェア アーキテクチャにできる、低精度の浮動小数点演算を実行して、パフォーマンスと精度の両方を犠牲にする必要があるのではなく c# を使用するより高い有効桁数の型すべての浮動小数点演算に使用されます。 正確な結果を提供する以外はこのことはほとんどありません、測定可能な影響を与えます。 ただし、フォームの式で`x * y / z`乗算が外部にある結果を生成します、`double`範囲が、後続の除算に一時的な結果を表示、`double`式であるという事実の範囲評価上の範囲で形式、無限値ではなく生成する有限の結果が発生する可能性があります。

### <a name="the-decimal-type"></a>10 進数型

`decimal` 型は 128 ビットのデータ型で、財務や通貨の計算に適しています。 `decimal`型は、範囲の値を表すことができます`1.0 * 10^-28`に約`7.9 * 10^28`28 ~ 29 桁を使用します。

型の値の有限のセット`decimal`の形式は`(-1)^s * c * 10^-e`ここで、符号`s`が 0 または 1、係数`c`で指定されます`0 <= *c* < 2^96`、および小数点以下桁数`e`はように`0 <= e <= 28`します。`decimal`符号付きのゼロ、無限大、または NaN の型をサポートしません。 A`decimal`は 10 の累乗によってスケーリング 96 ビット整数として表されます。 `decimal`絶対値を使用してより小さい`1.0m`値は 28 小数点に正確ながありません。 `decimal`絶対値より大きいまたは等しいを`1.0m`値が 28 または 29 桁までは正確です。 Contrary、`float`と`double`データ型、0.1 などの 10 進数の小数値で正確に表すことができます、`decimal`表現。 `float`と`double`表現、このような数値は多くの場合、無限の小数できるため、表現の丸めを受けやすいエラー。

型のかどうかは、二項演算子のオペランドの 1 つ`decimal`、もう一方のオペランドが整数型または型にする必要があります`decimal`します。 整数型のオペランドが存在する場合に変換されます`decimal`操作を実行する前にします。

型の値に演算の結果`decimal`から正確な結果 (維持拡張、オペレーターごとに定義されている) を計算して、表現に合わせて丸め処理し、なることができます。 結果が丸められる、最も近い表現可能な値は、し、結果が均等に最下位ビットが 0 (これと呼ばれる「銀行型丸め」) である値に 2 つの表現可能な値。 0 個の結果は、0 の符号とスケールは 0 を常に持ちます。

10 進数の算術演算の値以下と等しいかそれよりも場合`5 * 10^-29`絶対値で操作の結果はゼロになります。 場合、`decimal`算術演算が大きすぎて結果の生成、`decimal`形式、`System.OverflowException`がスローされます。

`decimal`型がより高い精度浮動小数点型よりも、小さい範囲です。 浮動小数点型から変換したがって、`decimal`オーバーフロー例外、および変換から生じる可能性があります`decimal`浮動小数点型の有効桁数の損失を伴う可能性があります。 これらの理由から、暗黙的な変換が存在しない浮動小数点型の間と`decimal`、明示的なキャストしないことはできません浮動小数点を混在させると、`decimal`同じ式のオペランド。

### <a name="the-bool-type"></a>Bool 型

`bool`型がブール論理の数量を表します。 型の有効な値`bool`は`true`と`false`します。

間の標準変換が存在しない`bool`および他の型。 具体的には、`bool`型は別に整数の型から独立して、`bool`整数値では、代わりに、その逆の値は使用できません。

C および C++ の言語で、ゼロ整数または浮動小数点の値または null ポインターをブール値に変換できる`false`、0 以外の整数または浮動小数点値または null 以外のポインターは、ブール値に変換できると`true`します。 C# で、このような変換を 0、整数または浮動小数点値を明示的に比較することで、または明示的にオブジェクト参照を比較することで行われます`null`します。

### <a name="enumeration-types"></a>列挙型

列挙型は、名前付き定数を使用して別個の型です。 すべての列挙型がある必要があります、基になる型`byte`、 `sbyte`、 `short`、 `ushort`、 `int`、 `uint`、`long`または`ulong`します。 列挙型の値のセットでは、基になる型の値のセットと同じです。 列挙型の値では、名前付き定数の値に限定されません。 列挙型が列挙体の宣言で定義されている ([列挙型宣言](enums.md#enum-declarations))。

### <a name="nullable-types"></a>Null 許容型

Null 許容型のすべての値を表すことができます、***基になる型***さらに null 値を追加します。 Null 許容型が書き込まれる`T?`ここで、`T`は、基になる型です。 この構文の短縮形は、 `System.Nullable<T>`、し、2 つの形式は同じ意味で使用されることができます。

A ***null 非許容値型***以外の任意の値型を逆には`System.Nullable<T>`とその省略`T?`(いずれかの`T`)、さらに (つまり、いずれかの null 非許容値型に制約されている任意の型パラメーターパラメーターの入力を`struct`制約)。 `System.Nullable<T>`型の値の型の制約を指定する`T`([パラメーターの制約入力](classes.md#type-parameter-constraints))、null 許容型の基になる型は任意の null 非許容値型であることを意味します。 Null 許容型の基になる型は、null 許容型または参照型にすることはできません。 たとえば、`int??`と`string?`種類が無効です。

Null 許容型のインスタンス`T?`は 2 つのパブリックな読み取り専用プロパティがあります。

*  A`HasValue`型のプロパティ `bool`
*  A`Value`型のプロパティ `T`

インスタンス`HasValue`が true は null 以外と呼ばれます。 Null 以外のインスタンスには、既知の値が含まれていますと`Value`その値を返します。

インスタンス`HasValue`は、false は null と呼ばれます。 Null インスタンスには、未定義の値があります。 読み取ろうとした、`Value`の null インスタンスにより、`System.InvalidOperationException`がスローされます。 アクセスするプロセス、 `Value` null 許容型のインスタンスのプロパティと呼びます***ラップ解除***します。

既定のコンス トラクター、すべての null 許容型だけでなく`T?`型の 1 つの引数を受け取るパブリック コンス トラクターを持つ`T`します。 値が指定される`x`型の`T`フォームのコンス トラクターの呼び出し

```csharp
new T?(x)
```
null 以外のインスタンスを作成します`T?`を`Value`プロパティは`x`します。 指定した値と呼びますの null 許容型の null 以外のインスタンスを作成するプロセス***ラッピング***します。

暗黙的な変換はから利用可能な`null`リテラルを`T?`([Null リテラル変換](conversions.md#null-literal-conversions)) との間`T`に`T?`([暗黙的な null 許容変換](conversions.md#implicit-nullable-conversions)).

## <a name="reference-types"></a>参照型

参照型は、クラス型、インターフェイス型、配列型、またはデリゲート型です。

```antlr
reference_type
    : class_type
    | interface_type
    | array_type
    | delegate_type
    ;

class_type
    : type_name
    | 'object'
    | 'dynamic'
    | 'string'
    ;

interface_type
    : type_name
    ;

array_type
    : non_array_type rank_specifier+
    ;

non_array_type
    : type
    ;

rank_specifier
    : '[' dim_separator* ']'
    ;

dim_separator
    : ','
    ;

delegate_type
    : type_name
    ;
```

参照型の値はへの参照、***インスタンス***として知ら、型の***オブジェクト***します。 特殊な値`null`すべての参照型と互換性が、インスタンスのないことを示します。

### <a name="class-types"></a>クラス型

クラス型では、データ メンバー (定数とフィールド)、関数メンバー (メソッド、プロパティ、イベント、インデクサー、演算子、インスタンス コンス トラクター、デストラクター、静的コンス トラクター) と入れ子にされた型を含むデータ構造を定義します。 クラス型では、継承、派生クラスが基底クラスを拡張および限定するためのメカニズムをサポートします。 使用してクラス型のインスタンスが作成された*object_creation_expression*s ([オブジェクト作成式](expressions.md#object-creation-expressions))。

クラス型が記載されて[クラス](classes.md)します。

特定の種類の定義済みのクラスは、次の表に示すように、c# 言語では、特別な意味を持ちます。


| __クラス型__     | __説明__                                         |
|--------------------|---------------------------------------------------------|
| `System.Object`    | その他のすべての種類の最終的な基本クラス。 参照してください[オブジェクトの種類](types.md#the-object-type)します。 | 
| `System.String`    | C# 言語の文字列型。 参照してください[文字列型](types.md#the-string-type)します。         |
| `System.ValueType` | すべての値型の基本クラス。 参照してください[、System.ValueType 型](types.md#the-systemvaluetype-type)します。          |
| `System.Enum`      | すべての列挙型の基本クラス。 参照してください[列挙型](enums.md)します。              |
| `System.Array`     | すべての配列型の基本クラス。 「[配列](arrays.md)」を参照してください。             |
| `System.Delegate`  | すべてのデリゲート型の基本クラス。 参照してください[デリゲート](delegates.md)します。          |
| `System.Exception` | すべての例外の種類の基本クラス。 参照してください[例外](exceptions.md)します。         |

### <a name="the-object-type"></a>オブジェクトの種類

`object`クラス型が他のすべての型の基本クラス。 すべての型 (C#) で直接的または間接的に派生から、`object`クラス型。

キーワード`object`は定義済みのクラスのエイリアスにすぎません`System.Object`します。

### <a name="the-dynamic-type"></a>動的な型

`dynamic`入力と、このような`object`、任意のオブジェクトを参照できます。 型の式に演算子を適用する`dynamic`、その解決方法は、プログラムが実行されるまでに遅延します。 そのため、オペレーターは、参照先オブジェクトに適用することはできません合法的に場合、エラーを指定しないコンパイル時にします。 代わりに実行時に、演算子の解決が失敗したときに例外がスローされます。

その目的はで詳しく説明されている、動的バインドを許可するのには、[動的バインド](expressions.md#dynamic-binding)します。

`dynamic` 同じと見なされます`object`次の点を除きます。

*  型の式に対して操作`dynamic`動的にバインドすることができます ([動的バインド](expressions.md#dynamic-binding))。
*  型の推論 ([型推論](expressions.md#type-inference)) が優先されます`dynamic`経由で`object`両方の候補にする場合。

この同等性のため、次の処理が保持されます。

*  間の暗黙的な id 変換が`object`と`dynamic`、および置換するときに、同じ構築された型の間で`dynamic`で `object`
*  明示的および暗黙的な変換との間`object`との間にも適用`dynamic`します。
*  置換するときに、同じメソッド シグネチャ`dynamic`で`object`は同じシグネチャと見なされます
*  型`dynamic`は区別できません`object`実行時にします。
*  型の式`dynamic`と呼ばれますが、***動的な式***します。

### <a name="the-string-type"></a>文字列型

`string`型がシール クラス型から直接継承される`object`します。 インスタンス、`string`クラスは Unicode 文字の文字列を表します。

値、`string`型は文字列リテラルとして記述できます ([文字列リテラル](lexical-structure.md#string-literals))。

キーワード`string`は定義済みのクラスのエイリアスにすぎません`System.String`します。

### <a name="interface-types"></a>インターフェイス型

インターフェイスは、コントラクトを定義します。 クラスまたはインターフェイスを実装する構造体は、そのコントラクトに従う必要があります。 複数の基底インターフェイスから継承でき、クラスまたは構造体には、複数のインターフェイスを実装できます。

インターフェイスの種類が記載されて[インターフェイス](interfaces.md)します。

### <a name="array-types"></a>配列型

配列は、算出されたインデックスを介してアクセスされる 0 個以上の変数を含むデータ構造です。 配列の要素とも呼ばれます。 配列に含まれる変数はすべて、同じ型と、この型には、配列の要素の型が呼び出されます。

配列型が記載されて[配列](arrays.md)します。

### <a name="delegate-types"></a>デリゲート型

デリゲートは、1 つまたは複数のメソッドを参照するデータ構造です。 インスタンスのメソッドも参照、対応するオブジェクトのインスタンス。

C または C++ では、デリゲートの最も近いは、関数ポインターが関数ポインターは、静的関数を参照できるのみ、一方デリゲートは両方の静的な参照しインスタンス メソッド。 後者の場合、デリゲートは、メソッドのエントリ ポイントへの参照だけでなく、メソッドの呼び出し元となるオブジェクトのインスタンスへの参照を格納します。

デリゲート型が記載されて[デリゲート](delegates.md)します。

## <a name="boxing-and-unboxing"></a>ボックス化とボックス化解除

ボックス化とボックス化解除の概念は、# の型システムの中心となります。 間のブリッジを提供*value_type*s と*reference_type*s のすべての値を許可、 *value_type*型との間に変換する`object`します。 ボックス化とボックス化解除は、任意の型の値を最終的として処理できるオブジェクトの型システムの統合ビューを使用できます。

### <a name="boxing-conversions"></a>ボックス化変換

ボックス化変換では、 *value_type*に暗黙的に変換する、 *reference_type*します。 次のボックス化変換は次のとおりです。

*  いずれかから*value_type*型に`object`します。
*  いずれかから*value_type*型に`System.ValueType`します。
*  いずれかから*non_nullable_value_type*いずれかに*interface_type*によって実装される、 *value_type*します。
*  いずれかから*nullable_type*いずれかに*interface_type*の基になる型によって実装される、 *nullable_type*します。
*  いずれかから*enum_type*型に`System.Enum`します。
*  いずれかから*nullable_type*基になると*enum_type*型に`System.Enum`します。
*  実行時に最終的には値型から参照型に変換する場合に、型パラメーターからの暗黙的な変換、ボックス化変換として実行されることに注意してください ([型パラメーターを使用する暗黙的な変換](conversions.md#implicit-conversions-involving-type-parameters))。

値をボックス化、 *non_nullable_value_type*オブジェクト インスタンスの割り当てとコピーから成る、 *non_nullable_value_type*値をそのインスタンスにします。

値をボックス化、 *nullable_type*である場合は、null 参照を生成、`null`値 (`HasValue`は`false`)、またはラップの解除とそれ以外の場合、基になる値をボックス化の結果。

実際の値をボックス化のプロセスを*non_nullable_value_type*を仮定すると、汎用の存在をわかりやすく説明***ボックス化クラス***、次のように宣言された場合と同様にします。

```csharp
sealed class Box<T>: System.ValueType
{
    T value;

    public Box(T t) {
        value = t;
    }
}
```

値のボックス化`v`型の`T`式を実行するようになりましたは`new Box<T>(v)`、型の値として生成されたインスタンスを返すと`object`します。 そのため、ステートメント
```csharp
int i = 123;
object box = i;
```
概念的に対応しています
```csharp
int i = 123;
object box = new Box<int>(i);
```

ようにボックス化クラス`Box<T>`以上が存在しない実際にボックス化された値の動的な型がクラス型では実際にはありません。 代わりに、型のボックス化された値`T`動的な型を持つ`T`を使用して動的な型チェックと、`is`演算子は単に型を参照`T`します。 例えば以下のようにします。
```csharp
int i = 123;
object box = i;
if (box is int) {
    Console.Write("Box contains an int");
}
```
文字列の出力は"`Box contains an int`"コンソール。

ボックス化変換では、ボックス化される値のコピーを意味します。 これは、別の変換から、 *reference_type*入力`object`、値引き続き同じインスタンスを参照すると、単に弱い派生型と見なさ`object`します。 たとえば、宣言について考えます。
```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
次のステートメント
```csharp
Point p = new Point(10, 10);
object box = p;
p.x = 20;
Console.Write(((Point)box).x);
```
に、コンソールで、値 10 が出力の割り当てで発生する暗黙的なボックス化操作`p`に`box`の値と、`p`をコピーします。 `Point`されて宣言されている、`class`代わりに、値 20 出力されるため、`p`と`box`同じインスタンスを参照します。

### <a name="unboxing-conversions"></a>ボックス化解除変換

ボックス化解除の変換では、 *reference_type*に明示的に変換する、 *value_type*します。 次のボックス化解除変換が存在します。

*  型から`object`いずれかに*value_type*します。
*  型から`System.ValueType`いずれかに*value_type*します。
*  いずれかから*interface_type*いずれかに*non_nullable_value_type*を実装する、 *interface_type*します。
*  いずれかから*interface_type*いずれかに*nullable_type*基になる型を実装して、 *interface_type*します。
*  型から`System.Enum`いずれかに*enum_type*します。
*  型から`System.Enum`いずれかに*nullable_type*基になると*enum_type*します。
*  実行時に最終的には、参照型から値型に変換する場合に、型パラメーターに明示的な変換、アンボックス変換として実行されることに注意してください ([動的の明示的な変換](conversions.md#explicit-dynamic-conversions))。

ボックス化解除の操作を*non_nullable_value_type*オブジェクト インスタンスのボックス化された値は、最初に確認から成る、指定された*non_nullable_value_type*、しの値をコピーして、インスタンス。

ボックス化解除、 *nullable_type*の null 値を生成、 *nullable_type*ソース オペランドの場合`null`、またはオブジェクト インスタンスの基になる型をボックス化解除のラップされた結果、*nullable_type*それ以外の場合。

オブジェクトのアンボックス変換、前のセクションで説明されている仮想のボックス化クラスを参照する`box`を*value_type* `T`式を実行から成る`((Box<T>)box).value`。 そのため、ステートメント
```csharp
object box = 123;
int i = (int)box;
```
概念的に対応しています
```csharp
object box = new Box<int>(123);
int i = ((Box<int>)box).value;
```

ボックス化解除変換の指定された*non_nullable_value_type*ソース オペランドの値が実行時に成功するをボックス化された値への参照をある必要があります*non_nullable_value_type*します。 ソース オペランドの場合`null`、`System.NullReferenceException`がスローされます。 ソース オペランドが、互換性のないオブジェクトへの参照の場合、`System.InvalidCastException`がスローされます。

ボックス化解除変換の指定された*nullable_type*に成功すると、実行時に、ソース オペランドの値はいずれかのことがあります`null`または基になるのボックス化された値への参照*non_nullable_value_type*の*nullable_type*します。 ソース オペランドが、互換性のないオブジェクトへの参照の場合、`System.InvalidCastException`がスローされます。

## <a name="constructed-types"></a>構築された型

ジェネリック型の宣言では、単独で表します、***ジェネリック型にバインドされていない***の適用を使用して、さまざまな種類のフォームに「ブルー プリント」として使用される***引数を入力***します。 型引数は山かっこ内に書き込まれます (`<`と`>`) すぐに次のジェネリック型の名前。 少なくとも 1 つの型引数を含む型が呼び出される、***構築型***します。 構築された型は、型名の表示に使用できる言語のほとんどの場所で使用できます。 バインドされていないのジェネリック型は内でのみ使用できます、 *typeof_expression* ([typeof 演算子](expressions.md#the-typeof-operator))。

単純な名前として式で構築された型は使用もできます ([簡易名](expressions.md#simple-names)) メンバーにアクセスするとき、または ([メンバー アクセス](expressions.md#member-access))。

ときに、 *namespace_or_type_name*と見なされますパラメーター型の正しい数と種類の評価された、汎用のみです。 そのため、型がある型パラメーターの数が異なる限り、さまざまな種類を識別するために同じ識別子を使用することです。 これは、機能は、同じプログラム内でジェネリックと非ジェネリックのクラスが混在する場合に便利です。

```csharp
namespace Widgets
{
    class Queue {...}
    class Queue<TElement> {...}
}

namespace MyApplication
{
    using Widgets;

    class X
    {
        Queue q1;            // Non-generic Widgets.Queue
        Queue<int> q2;       // Generic Widgets.Queue
    }
}
```

A *type_name*型パラメーターを直接指定しない場合でも、構築された型を識別する可能性があります。 これは、問題は、型がジェネリック クラス宣言の中で入れ子になった、外側の宣言のインスタンスの型が暗黙的に名前参照の使用に発生することができます ([ジェネリック クラスの型を入れ子になった](classes.md#nested-types-in-generic-classes))。

```csharp
class Outer<T>
{
    public class Inner {...}

    public Inner i;                // Type of i is Outer<T>.Inner
}
```

アンセーフ コードで構築された型として使用できません、 *unmanaged_type* ([ポインター型](unsafe-code.md#pointer-types))。

### <a name="type-arguments"></a>型引数

型引数リストの各引数は単に、*型*します。

```antlr
type_argument_list
    : '<' type_arguments '>'
    ;

type_arguments
    : type_argument (',' type_argument)*
    ;

type_argument
    : type
    ;
```

安全でないコード ([アンセーフ コード](unsafe-code.md))、 *type_argument*ポインター型ができない可能性があります。 各型引数の型パラメーターの対応する、制約を満たす必要があります ([パラメーターの制約入力](classes.md#type-parameter-constraints))。

### <a name="open-and-closed-types"></a>オープン型

すべての種類は、いずれかに分類できます***の種類を開く***または***クローズ型***します。 オープン型は、型パラメーターを含む型です。 具体的には。

*  型パラメーターは、オープン型を定義します。
*  要素の型がオープン型場合にのみ、配列型はオープン型が。
*  構築された型はオープン型がオープン型の 1 つ以上の型引数の場合にのみ 構築された入れ子にされた型はオープン型がオープン型の 1 つ以上の型引数または外側の型の型引数の場合にのみ

閉じている型は、オープン型ではない型です。

実行時にには、ジェネリック宣言する型引数を適用することによって作成された構築されたクローズ型のコンテキストでのジェネリック型の宣言内でコードをすべて実行されます。 ジェネリック型内の各型パラメーターは、特定の実行時の型にバインドされます。 すべてのステートメントと式の実行時の処理は、常には、クローズ型が発生し、コンパイル時にのみ発生するオープン型が処理します。

構築されたクローズ型をそれぞれが、独自の静的変数は、クローズ構築型と共有されていないセット。 実行時にオープン型が存在しないために、オープン型に関連付けられている静的変数はありません。 2 つのクローズ構築型は、同じバインドされていないジェネリック型から構築されると、対応する型引数は、同じ型である場合、同じ型です。

### <a name="bound-and-unbound-types"></a>バインドとバインドされていない型

用語***型にバインドされていない***非ジェネリック型または非バインドのジェネリック型を表します。 用語***型にバインドされている***非ジェネリック型または構築された型を表します。

バインドされていない型は、型宣言で宣言されたエンティティを指します。 非バインドのジェネリック型自体ではなく、型と、変数、引数または戻り値の型として、または基本データ型として使用できません。 バインドされていないジェネリック型を参照できる唯一のコンストラクトを`typeof`式 ([typeof 演算子](expressions.md#the-typeof-operator))。

### <a name="satisfying-constraints"></a>制約の充足

構築された型またはジェネリック メソッドを参照すると、指定された型引数はジェネリック型またはメソッドで宣言された型パラメーターの制約に照らしてチェック ([パラメーターの制約入力](classes.md#type-parameter-constraints))。 各`where`句では、型引数`A`名前付きに対応する型パラメーターは、次のように制約に照らしてチェックします。

*  クラス型、インターフェイス型または型パラメーターの制約である場合、`C`制約に表示される任意の型パラメーターに指定された型引数の制約が置き換えことを表します。 制約を満たす必要がありますは、型`A`は型に変換`C`次のいずれか。
    * Id 変換 ([恒等変換](conversions.md#identity-conversion))
    * 暗黙の参照変換 ([暗黙の参照変換](conversions.md#implicit-reference-conversions))
    * ボックス化変換 ([ボックス化変換](conversions.md#boxing-conversions))、その型 A は、null 非許容値型。
    * 型パラメーターから暗黙の参照、ボックス化、または型パラメーター変換`A`に`C`します。
*  制約が参照型の制約である場合 (`class`)、型`A`次のいずれかを満たす必要があります。
    * `A` インターフェイス型、クラス型、デリゲート型または配列型。 なお`System.ValueType`と`System.Enum`はこの制約を満たす参照型です。
    * `A` 参照型があることがわかっている型パラメーターは、([パラメーターの制約入力](classes.md#type-parameter-constraints))。
*  制約が値型の制約である場合 (`struct`)、型`A`次のいずれかを満たす必要があります。
    * `A` 構造体の型または列挙型を null 許容型ではなくです。 なお`System.ValueType`と`System.Enum`はこの制約を満たしていない参照型です。
    * `A` 値型の制約を持つ型パラメーター ([パラメーターの制約入力](classes.md#type-parameter-constraints))。
*  制約は、コンス トラクター制約が場合`new()`、型`A`必要があります`abstract`パブリック パラメーターなしのコンス トラクターが必要とします。 これが満たされるは、次のいずれかが true の場合。
    * `A` 値の型は、すべての値型がパブリックの既定のコンス トラクターがあるため ([既定のコンス トラクター](types.md#default-constructors))。
    * `A` コンス トラクター制約を持つ型パラメーター ([パラメーターの制約入力](classes.md#type-parameter-constraints))。
    * `A` 値型の制約を持つ型パラメーター ([パラメーターの制約入力](classes.md#type-parameter-constraints))。
    * `A` ないクラス`abstract`明示的に宣言が含まれている`public`パラメーターなしコンス トラクター。
    * `A` ない`abstract`既定のコンス トラクター ([既定のコンス トラクター](classes.md#default-constructors))。

指定された型引数によって 1 つ以上の型パラメーターの制約が満たされない場合、コンパイル時エラーが発生します。

制約はありませんので、型パラメーターは継承されず、いずれかを継承します。 次の例で`D`、型パラメーターに制約を指定する必要がある`T`ように`T`基底クラスによって課される制約を満たす`B<T>`します。 これに対し、クラス`E`ために、制約を指定する必要があります`List<T>`実装`IEnumerable`は`T`します。

```csharp
class B<T> where T: IEnumerable {...}

class D<T>: B<T> where T: IEnumerable {...}

class E<T>: B<List<T>> {...}
```

## <a name="type-parameters"></a>型パラメーター

型パラメーターでは、値型またはパラメーターは、実行時にバインドされている参照型を指定する識別子です。

```antlr
type_parameter
    : identifier
    ;
```

型パラメーターは、多くのさまざまな実際の型引数でインスタンス化することができます、ために、型パラメーターには、若干異なる操作とその他の型よりも制限があります。 不足している機能には次が含まれます。

*  型パラメーターを使用して直接基底クラスを宣言することはできません ([基本クラス](classes.md#base-class)) またはインターフェイス ([バリアント型パラメーター リスト](interfaces.md#variant-type-parameter-lists))。
*  パラメーターは、存在する場合、制約とは異なります。 型のメンバー検索の規則は、型パラメーターに適用されます。 詳細については、[メンバー ルックアップ](expressions.md#member-lookup)します。
*  使用可能な変換型パラメーターは、存在する場合、制約に依存しているは、型パラメーターに適用されます。 詳細については、[型パラメーターを使用する暗黙的な変換](conversions.md#implicit-conversions-involving-type-parameters)と[動的の明示的な変換](conversions.md#explicit-dynamic-conversions)します。
*  リテラル`null`型パラメーターが参照型であることがわかっている場合を除き、型パラメーターで指定する型に変換できません ([型パラメーターを使用する暗黙的な変換](conversions.md#implicit-conversions-involving-type-parameters))。 ただし、`default`式 ([既定の値式](expressions.md#default-value-expressions)) 代わりに使用できます。 さらに、型パラメーターで指定した型の値と比較できる`null`を使用して`==`と`!=`([参照型の等値演算子](expressions.md#reference-type-equality-operators)) 型パラメーターに値型の制約がない限り、します。
*  A`new`式 ([オブジェクト作成式](expressions.md#object-creation-expressions)) によって、型パラメーターが制約される場合のみの型パラメーターに使用できます、 *constructor_constraint*または値制約 (を入力します。[パラメーターの制約入力](classes.md#type-parameter-constraints))。
*  型パラメーターは、属性内の任意の場所で使用できません。
*  メンバー アクセスでは、型パラメーターを使用できません ([メンバー アクセス](expressions.md#member-access)) または型の名前 ([Namespace と型の名前](basic-concepts.md#namespace-and-type-names)) 静的メンバーまたは入れ子にされた型を識別するためにします。
*  としてアンセーフ コードは、型パラメーターを使用することはできません、 *unmanaged_type* ([ポインター型](unsafe-code.md#pointer-types))。

型パラメーター、型では、純粋なコンパイル時の構成要素があります。 実行時に、それぞれの型パラメーターは、ジェネリック型の宣言する型引数を指定することによって指定された実行時の型にバインドされます。 したがって、変数の型を宣言で型パラメーターは、実行時に、クローズ構築型になります ([オープンとクローズ型](types.md#open-and-closed-types))。 すべての型パラメーターを含む式とステートメントの実行時の実行は、そのパラメーターの型引数として渡された実際の型を使用します。

## <a name="expression-tree-types"></a>式ツリー型

***式ツリー***実行可能コードではなく、データ構造として表されるラムダ式を許可します。 式ツリーは値の***式ツリー型***フォームの`System.Linq.Expressions.Expression<D>`ここで、`D`はデリゲート型。 短縮形を使用してこれらの型をこの仕様の残りの部分参照`Expression<D>`します。

変換が存在するかどうか、ラムダ式からデリゲート型に`D`、変換は、式ツリー型にも存在する`Expression<D>`します。 ラムダ式をデリゲート型の変換では、ラムダ式の実行可能コードを参照するデリゲートを生成する一方、式ツリー型への変換は、ラムダ式の式ツリー表現を作成します。

式ツリーは、ラムダ式の効率的なメモリ内データ表現および透過的と明示的なラムダ式の構造を作成します。

デリゲート型と同様に`D`、`Expression<D>`パラメーターと戻り値の型があるといいますのものと同じである`D`します。

次の例では、実行可能コードと、式ツリーの両方に、ラムダ式を表します。 変換が存在するため`Func<int,int>`、変換が存在する`Expression<Func<int,int>>`:

```csharp
Func<int,int> del = x => x + 1;                    // Code

Expression<Func<int,int>> exp = x => x + 1;        // Data
```

次のデリゲートのこれらの割り当て`del`を返すメソッドを参照`x + 1`、式ツリーと`exp`式を記述するデータ構造を参照`x => x + 1`。

ジェネリック型の正確な定義`Expression<D>`ラムダ式を式ツリー型に変換する場合は、式ツリーを構築するための正確なルールとは、どちらもこの仕様の範囲外です。

2 つのことを明示する重要なのとおりです。

*  すべてのラムダ式は、式ツリーに変換できます。 たとえば、ステートメントの本体でラムダ式と代入式を含むラムダ式を表示することはできません。 このような場合は、変換が存在しますはコンパイル時に失敗します。 これらの例外の詳細については[匿名関数の変換](conversions.md#anonymous-function-conversions)します。
*   `Expression<D>` インスタンス メソッドを提供しています`Compile`型のデリゲートを生成する`D`:。

    ```csharp
    Func<int,int> del2 = exp.Compile();
    ```

    このデリゲートの呼び出しが実行される式ツリーによって表されるコードです。 したがって、上記の定義を指定するには、del と del2 はそれと同等と、次の 2 つのステートメントは同じ効果があります。

    ```csharp
    int i1 = del(1);
    
    int i2 = del2(1);
    ```

    このコードを実行した後`i1`と`i2`どちらにも値`2`します。

