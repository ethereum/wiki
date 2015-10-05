_исходный текст [Solidity Tutorial](https://github.com/ethereum/wiki/wiki/Solidity-Tutorial), 31 августа 2015_
***
Solidity является высокоуровневым языком, синтаксис которого схож c JavaScript, и он разработан для компиляции кода для Виртуальной машины Ethereum. Это учебное руководство обеспечивает основное введение в Solidity и предполагает некоторое знание Виртуальной машины Ethereum и программирования в целом. Оно не касается таких функций как спецификация естественного языка <https://github.com/ethereum/wiki/wiki/Solidity-Tutorial/Ethereum-Natural-Specification-Format> или формальная верификация и не является заключительной спецификаций языка.

Можно начать использовать Solidity в браузере <http://chriseth.github.io/cpp-ethereum> без потребности загрузжать или компилировать что-либо. Это приложение только поддерживает компиляцию - если Вы хотите выполнить код или ввести его в блокчейн, необходимо использовать клиент как Geth <https://github.com/ethereum/go-ethereum> или AlethZero <https://github.com/ethereum/alethzero>.


**Быстрые ссылки:**

- [Шпаргалка](#cheatsheet)
- [Подсказки и Приемы](#tips-and-tricks)

**Оглавление**

<!-- TOC depth:6 withLinks:1 updateOnSave:1 -->
- [Некоторые примеры](#some-examples)
	- [Хранилище](#storage)
	- [Пример подвалюты](#subcurrency-example)
- [Разметка исходного файла Solidity](#layout-of-a-solidity-source-file)
- [Структура договора Solidity](#structure-of-a-solidity-contract)
- [Типы](#types)
	- [Элементарные типы (типы значений)](#elementary-types-value-types)
	- [Операторы, включающие LValues](#operators-involving-lvalues)
	- [Преобразования между элементарными типами](#conversions-between-elementary-types)
		- [Неявные преобразования](#implicit-conversions)
		- [Явные преобразования](#explicit-conversions)
		- [Тип возвращаемых значений](#type-deduction)
	- [Функции на адресах](#functions-on-addresses)
	- [Перечисления(Enums)](#enums)
	- [Ссылочные типы](#reference-types)
		- [Расположение данных](#data-location)
		- [Массивы](#arrays)
		- [Структуры](#structs)
- [Синтаксический сахар и глобально доступные переменные](#syntactic-sugar-and-globally-available-variables)
	- [Эфир и единицы измерения времени](#ether-and-time-units)
	- [Специальные переменные и функции](#special-variables-and-functions)
		- [Блок и свойства транзакции](#block-and-transaction-properties)
		- [Криптографические функции](#cryptographic-functions)
		- [Касательно контракта](#contract-related)
- [Управляющие структуры](#control-structures)
	- [Вызовы функции](#function-calls)
		- [Внутренние вызовы функции](#internal-function-calls)
		- [Внешние вызовы функции](#external-function-calls)
		- [Названные и дополнительные параметры функции](#named-and-optional-function-parameters)
	- [Порядок оценки выражений](#order-of-evaluation-of-expressions)
	- [Присвоение](#assignment)
	- [Исключения](#exceptions)
- [Договоры](#contracts)
	- [Взаимодействие через интерфейс с другими контрактами](#interfacing-with-other-contracts)
	- [Библиотеки](#libraries)_(todo)_
	- [Параметры конструктора](#constructor-arguments)
	- [Наследование контракта](#contract-inheritance)
		- [Параметры для конструкторов базового контракта](#arguments-for-base-constructors)
		- [Множественное наследование и линеаризация](#multiple-inheritance-and-linearization)
	- [Абстрактные контракты](#abstract-contracts)
	- [Спецификаторы видимости](#visibility-specifiers)
	- [Функции доступа](#accessor-functions)
	- [Функции нейтрализации](#fallback-functions)
	- [Функции модификаторов](#function-modifiers)
	- [Контракты](#constants)
	- [События](#events)
		- [Дополнительные ресурсы для понимания событий:](#additional-resources-for-understanding-events)
- [Разное](#miscellaneous)
	- [Разметка переменных состояния в хранилище](#layout-of-state-variables-in-storage)
	- [Скрытые особенности](#esoteric-features)
	- [Внутреннее - Оптимизатор](#internals-the-optimizer)
	- [Использование компилятора командной строки](#using-the-commandline-compiler)
	- [Подсказки и Приемы](#tips-and-tricks)
	- [Ловушки](#pitfalls)
	- [Шпаргалка](#cheatsheet)
		- [Глобальные переменные](#global-variables)
		- [Спецификаторы видимости для функции](#function-visibility-specifiers)
		- [Модификаторы](#modifiers)
		- [Типы](#types)


***

_Большое спасибо General-Beck Денис Солдатову за перевод._

_Note: This page is under construction_
