<!--
SPDX-FileCopyrightText: Copyright 2026 katakishi0
SPDX-License-Identifier: GPL-3.0-or-later
-->

# Xbox One Fat — protótipo experimental

Esta branch investiga a compatibilidade do port Eden UWP/D3D12 com o Xbox One Fat (modelo original, 2013). **Não declara suporte ao console nem promete jogos jogáveis.** O upstream documenta testes em Xbox Series X|S; seu backend foi ajustado primeiro para esse alvo.

## Primeiro ajuste

O backend deixa de assumir Shader Model 6.4. Ele usa o maior Shader Model 6.x informado pelo dispositivo, limitado a 6.4, que é o teto suportado pelo tradutor DXIL incluído. Se o dispositivo reportar somente Shader Model 5.1 (ou outro valor inferior a 6.0), o compilador DXIL acelerado fica indisponível e escreve uma mensagem clara no log, em vez de tentar compilar shaders com uma capacidade que não foi detectada.

Essa mudança é apenas uma **detecção/condição de compatibilidade**. Ela não converte o backend para DXBC/Shader Model 5.1, não prova que o Xbox One Fat oferece DXIL compatível, e não resolve limitações de CPU, memória, GPU, JIT ou APIs UWP.

## Portões de validação

1. Compilar e empacotar x64/UWP com o SDK e toolchain Windows suportados pelo projeto.
2. Instalar em Dev Mode e confirmar ativação, janela/swap chain, input de gamepad e gravação do log de capacidades.
3. Verificar o Shader Model e os demais recursos D3D12 reportados pelo Xbox One Fat.
4. Se o Shader Model ficar abaixo de 6.0, decidir entre implementar um backend de shaders compatível (trabalho substancial) ou encerrar a rota D3D12 atual para esse hardware.
5. Se o shader path inicializar, testar um frame de limpeza/blit e só então seguir para shaders Maxwell, ASTC, caches e sincronização.
6. Medir uso de memória, tempo por frame e estabilidade antes de testar conteúdo homebrew próprio ou dumps que o usuário tenha direito de usar.

## Limites de verificação

A validação de código Windows/UWP exige ambiente Windows com Visual Studio/SDK adequados. A validação de instalação, APIs e performance exige acesso ao próprio Xbox One Fat em Dev Mode. Compilação no PC, mesmo que passe, não comprova compatibilidade no console.
