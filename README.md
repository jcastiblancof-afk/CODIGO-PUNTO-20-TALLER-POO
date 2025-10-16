# CODIGO-PUNTO-20-TALLER-POO
Taller: POO y modificadores de acceso en Python  
Instrucciones 
•	Lee cada fragmento, ejecuta mentalmente el código y responde lo que se pide. 
•	Recuerda: en Python no hay “modificadores” como en Java/C++; se usan convenciones: o Público: nombre o Protegido (convención): _nombre 
o Privado (name mangling): __nombre se convierte a _<Clase>__nombre 
•	No edites el código salvo que la pregunta lo solicite. 
 
Parte A. Conceptos y lectura de código 
1) Selección múltiple  Dada la clase: class A:     x = 1     _y = 2 
    __z = 3 
 
a = A()  
¿Cuáles de los siguientes nombres existen como atributos accesibles directamente desde a?  A) A) a.x 
B)	a._y 
C)	a.__z 
D)	a._A__z 
 (a.x) es un atributo de tipo publico, existe como atributo accesible.
(a._y) es un atributo de tipo protegido, también es accesible dentro de la clase o de subclases.
En el caso del uso de doble guion bajo (__atributo) no crea atributos verdaderamente privados, sino que activa el name mangling, es decir el cambio automático de nombre a la forma (_<Clase>__atributo).
Este mecanismo no impide el acceso externo, pero reduce los conflictos en la herencia y protege los atributos de ser sobrescritos accidentalmente.
2) Salida del programa class A:     def __init__(self):         self.__secret = 42 
 
a = A() 
print(hasattr(a, '__secret'), hasattr(a, '_A__secret'))  
¿Qué imprime? 
Imprime “False True” El atributo __secret no se encuentra porque Python lo renombra internamente a _A__secret. Por eso, el primer hasattr da False y el segundo da True.
 
3) Verdadero/Falso (explica por qué) 
a)	El prefijo _ impide el acceso desde fuera de la clase. 
b)	El prefijo __ hace imposible acceder al atributo. 
c)	El name mangling depende del nombre de la clase. 
RESPUESTA// 
C. El atributo __x se transforma en _ClassName__x — por eso el nombre de la clase es parte del nombre final. (Regla: el prefijo de la clase en el mangling se basa en el nombre de la clase donde se definió el atributo). 
4)	Lectura de código 
class Base:
    def __init__(self):
        self._token = "abc"
class Sub(Base):
    def reveal(self):
        return self._token

print(Sub().reveal())
¿Qué se imprime y por qué no hay error de acceso? 
_token está declarado con un único guion bajo (_token), que es convención de “protegido”, no una restricción. La subclase hereda el atributo y puede acceder a él sin error. El guion bajo no cambia el nombre del atributo, solo sugiere un uso interno.
IMPRIME: abc
 
4)	Name mangling en herencia 
class Base:     def __init__(self):         self.__v = 1 
 
class Sub(Base):     def __init__(self):         super().__init__()         self.__v = 2     def show(self): 
        return (self.__v, self._Base__v) 
 
print(Sub().show())  
¿Cuál es la salida? 
(self.__v) en Base se cambia a (self. Base__v) (valor 1).
(self.__v) en sub se cambia a (self. sub__v) (valor 2).
Son dos atributos distintos en la misma instancia porque el cambio incluye el nombre de la clase donde se definió. En show() (self.__v) → (_Sub__v (2)) y (self._Base__v) → (1).
SALIDA: (2, 1)
 
6)	Identifica el error 
class Caja: 
    __slots__ = ('x',) 
     c = Caja() c.x = 10 
c.y = 20  
¿Qué ocurre y por qué? 
RTA//
 Al ejecutar c.y = 20 se lanza el error:  (AttributeError: 'Caja' object has no attribute 'y')
__slots__ limita los atributos que pueden añadirse dinámicamente a las instancias (aquí sólo 'x'). Intentar asignar y no permitido provoca AttributeError. __slots__ se usa para ahorrar memoria y controlar la API de atributos. (Si se quiere permitir nuevos atributos habría que incluir '__dict__' en __slots__ o no usar __slots__.)
7)	Rellenar espacios 
 Completa para que b tenga un atributo “protegido por convención”. class B:     def __init__(self):         self ______ = 99  
Escribe el nombre correcto del atributo. 
 self._valor = 99
un atributo “protegido por convención” se nombra con un guion bajo inicial (_nombre). Cualquier identificador con un solo _ al inicio cumple esa convención
8)	Lectura de métodos “privados” class M:     def __init__(self):         self._state = 0 
 
    def _step(self):         self._state += 1 
        return self._state 
 
    def __tick(self): 
        return self._step() 
 
m = M() print(hasattr(m, '_step'), hasattr(m, '__tick'), hasattr(m, '_M__tick')) 
 
¿Qué imprime y por qué? 
IMPRIME: True False True
Porque _step es público/convención “protegido”: existe → hasattr(m, '_step') es True. (el primer True), __tick fue definido con doble guion bajo, por lo que se cambia a _M__tick internamente; por eso hasattr(m, '__tick') es False (el segundo False), (no existe literal __tick) y hasattr(m, '_M__tick') es True (el tercer y utlimo true). Así se demuestra que métodos con __ están accesibles mediante su nombre mangled.
 
9) Acceso a atributos privados class S:     def __init__(self): 
        self.__data = [1, 2]     def size(self): 
        return len(self.__data) 
 
s = S() 
# Accede a __data (solo para comprobar), sin modificar el código de la clase: 
# Escribe una línea que obtenga la lista usando name mangling y la imprima.  
Escribe la línea solicitada. 
RTA// print(s._S__data)
(__data) se transforma internamente a (_S__data) (clase S), por eso podemos acceder con (s._S__data.) Esto demuestra que el doble guion bajo no elimina el atributo, solo lo renombra para evitar choques accidentales.
 


10) Comprensión de dir y mangling class D:     def __init__(self):         self.__a = 1         self._b = 2 
        self.c = 3 
 
d = D() 
names = [n for n in dir(d) if 'a' in n] print(names)  
¿Cuál de estos nombres es más probable que aparezca en la lista: __a, _D__a o a? Explica. 
RTA// _D__a es la más probable que aparezca.
__a fue cambiada por Python a _D__a (incorporando el nombre de la clase). dir(d) lista los nombres reales del objeto, así que verá la forma mangled _D__a. No aparecerá un nombre literal __a (porque ya fue reemplazado) ni a. Por eso la entrada con 'a' en dir(d) será algo como '_D__a'.
1.	Saldo nunca negativo
a.	CÓDIGO BASE:
 
b.	CÓDIGO RESUELTO:
class Cuenta:
    def __init__(self, saldo):
        self._saldo = 0
        self.saldo = saldo  

    @property
    def saldo(self):
        return self._saldo

    @saldo.setter
    def saldo(self, value):
        if value < 0:
            raise ValueError("El saldo no puede ser negativo")
        self._saldo = value
2.	Convierte temperatura_f en un atributo de solo lectura que se calcula desde temperatura_c.
a.	CÓDIGO BASE:
 
b.	CÓDIGO RESUELTO:
class Termometro:
    def __init__(self, temperatura_c):
        self._c = float(temperatura_c)

    @property
    def temperatura_f(self):
        return self._c * 9/5 + 32

3.	Haz que nombre sea siempre str. Si asignan algo que no sea str, lanza TypeError.
a.	CÓDIGO BASE:
 
b.	CÓDIGO RESUELTO:
class Usuario:
    def __init__(self, nombre):
        self.nombre = nombre  

    @property
    def nombre(self):
        return self._nombre

    @nombre.setter
    def nombre(self, value):
        if not isinstance(value, str):
            raise TypeError("El nombre debe ser una cadena (str)")
        self._nombre = value
4.	Expón una vista de solo lectura de una lista interna.
a.	CÓDIGO BASE:
 
b.	CÓDIGO RESUELTO:
class Registro:
    def __init__(self):
        self.__items = []

    def add(self, x):
        self.__items.append(x)

    @property
    def items(self):
        return tuple(self.__items)  
5.	Refactoriza para evitar acceso directo al atributo y validar que velocidad sea entre 0 y 200.
a.	CÓDIGO BASE:
 
b.	CÓDIGO RESUELTO:
class Motor:
    def __init__(self, velocidad):
        self._velocidad = 0          
        self.velocidad = velocidad 

    @property
    def velocidad(self):
        return self._velocidad

    @velocidad.setter
    def velocidad(self, value):
        if not (0 <= value <= 200):
            raise ValueError("La velocidad debe estar entre 0 y 200")
        self._velocidad = value
6.	Explica con tus palabras cuándo usarías _atributo frente a __atributo en una API pública de una librería.
a.	RESPUESTA: En Python cuando uso un atributo con un solo guion bajo (_atributo), lo hago como una forma de advertencia, es decir estoy diciendo que ese dato es interno de la clase y que aunque se pueda acceder, no debería usarse directamente, en cambio, si le pongo doble guion bajo (__atributo) ya no es solo una convención, porque Python cambia el nombre internamente y se vuelve más difícil de acceder desde afuera, además de que así evito choques de nombres cuando trabajo con herencia. En pocas palabras, uso _ para señalar que algo no es parte de la API pública y __ cuando quiero protegerlo de verdad.
7.	¿Qué problema hay aquí?
a.	CODIGO BASE:
 
b.	CÓDIGO RESUELTO:
-	Problema: El método get_data() devuelve la lista interna _data directamente

class Buffer:
    def __init__(self, data):
        self._data = list(data)
    def get_data(self):
        return tuple(self._data)   
-	Se devuelve una tupla inmutable solo para que se pueda leer y no modificar
8.	¿Dónde fallará esto y cómo lo arreglas?
a.	CÓDIGO BASE:
 
 
En la clase A, el atributo __x se transforma por name mangling en _A__x.
En la clase B, cuando se escribe self.__x, NO es el mismo atributo, ahi se transforma en _B__x
b.	CÓDIGO RESUELTO:
class A:
    def __init__(self):
        self._x = 1

class B(A):
    def get(self):
        return self._x


9.	Completa para exponer solo un método seguro de un objeto interno.
a.	CÓDIGO BASE
	 
b.	SOLUCIÓN:
class Servicio:
    def __init__(self):
        self.__repo = _Repositorio()   # objeto interno

    def guardar(self, k, v):
        self.__repo.guardar(k, v)      # delega en el repositorio

10.	Escribe una clase ContadorSeguro con
a.	REQUERIMIENTOS:
 
b.	SOLUCIÓN:
class ContadorSeguro:
    def __init__(self):
        self._n = 0   # atributo protegido
    def inc(self):
        self._n += 1
        self.__log()  # se llama al método privado cada vez que se incrementa

    @property
    def n(self):
        return self._n   # solo lectura

    def __log(self):
        print("tick")


