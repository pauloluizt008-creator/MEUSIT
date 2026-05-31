import { useEffect, useState } from 'react';

interface Premio {
  texto: string;
  valor: number;
  bonus: boolean;
  setor: number;
}

export default function GoldenSpinTablet() {
  const [girando, setGirando] = useState(false);
  const [angulo, setAngulo] = useState(0);
  const [resultado, setResultado] = useState('🎯 Gire a roleta e desbloqueie prêmios');
  const [saldo, setSaldo] = useState(0);
  const [saldoBonus, setSaldoBonus] = useState(5);
  const [girosRestantes, setGirosRestantes] = useState(3);
  const [jogadas, setJogadas] = useState(0);
  const [nomeSaque, setNomeSaque] = useState('');
  const [tipoPix, setTipoPix] = useState('');
  const [chavePix, setChavePix] = useState('');
  const [pixGerado, setPixGerado] = useState(false);
  const [tempoRestante, setTempoRestante] = useState(0);

  const saqueMinimo = 30;

  const premios: Premio[] = [
    { texto: '🎉 Ganhou R$ 20', valor: 20, bonus: false, setor: 0 },
    { texto: '🎉 Ganhou R$ 22', valor: 22, bonus: false, setor: 1 },
    { texto: '🎉 Ganhou R$ 25', valor: 25, bonus: false, setor: 2 },
    { texto: '😢 Não foi dessa vez', valor: 0, bonus: false, setor: 3 },
  ];

  const girarRoleta = () => {
    if (girando || girosRestantes <= 0) return;

    setGirando(true);

    let premio: Premio;

    // primeiros 2 giros fixos
    if (jogadas === 0) {
      premio = {
        texto: '🎉 Ganhou R$ 8',
        valor: 8,
        bonus: false,
        setor: 0,
      };
    } else if (jogadas === 1) {
      premio = {
        texto: '🎉 Ganhou R$ 15',
        valor: 15,
        bonus: false,
        setor: 1,
      };
    } else {
      const sorteio = Math.random();

      if (sorteio < 0.35) {
        premio = premios[0]; // 20
      } else if (sorteio < 0.65) {
        premio = premios[1]; // 22
      } else if (sorteio < 0.90) {
        premio = premios[2]; // 25
      } else {
        premio = premios[3]; // não foi dessa vez
      }

      // impede atingir 30 automaticamente
      if (saldo + premio.valor > 29) {
        premio = premios[3];
      }
    }

    const anguloSetor = 90;
    const anguloFinal = premio.setor * anguloSetor;
    const rotacao = 1440 + (360 - anguloFinal);

    setAngulo((prev) => prev + rotacao);
    setGirosRestantes((prev) => prev - 1);
    setJogadas((prev) => prev + 1);

    setTimeout(() => {
      setGirando(false);
      setResultado(premio.texto);

      if (premio.valor > 0) {
        setSaldo((prev) => prev + premio.valor);
      }
    }, 4000);
  };

  const gerarPix = () => {
    setPixGerado(true);
    setTempoRestante(23 * 60 * 60 + 59 * 60);
  };

  const copiarPix = async () => {
    try {
      await navigator.clipboard.writeText('18884375614');
      setResultado('📋 Chave PIX copiada com sucesso!');
    } catch {
      setResultado('❌ Não foi possível copiar');
    }
  };

  useEffect(() => {
    if (!pixGerado || tempoRestante <= 0) return;

    const timer = setInterval(() => {
      setTempoRestante((prev) => {
        if (prev <= 1) {
          setPixGerado(false);
          return 0;
        }

        return prev - 1;
      });
    }, 1000);

    return () => clearInterval(timer);
  }, [pixGerado, tempoRestante]);

  const formatarTempo = (segundos: number) => {
    const horas = Math.floor(segundos / 3600)
      .toString()
      .padStart(2, '0');

    const minutos = Math.floor((segundos % 3600) / 60)
      .toString()
      .padStart(2, '0');

    const seg = (segundos % 60).toString().padStart(2, '0');

    return `${horas}:${minutos}:${seg}`;
  };

  const handleSaque = () => {
    if (!nomeSaque || !tipoPix || !chavePix) {
      setResultado('⚠ Preencha os dados de saque');
      return;
    }

    if (saldo < saqueMinimo) {
      setResultado('⚠ Saldo insuficiente');
      return;
    }

    setSaldo((prev) => prev - saqueMinimo);
    setResultado('✅ Saque solicitado com sucesso!');
  };

  return (
    <div className="min-h-screen bg-black flex items-center justify-center px-3 py-4 text-white">
      <div className="w-full max-w-[500px] bg-zinc-950 border border-yellow-500/10 rounded-[38px] overflow-hidden shadow-[0_0_60px_rgba(255,215,0,0.08)]">
        <div className="bg-gradient-to-b from-yellow-500/20 to-transparent p-5 border-b border-zinc-800 text-center">
          <div className="text-5xl mb-3 animate-pulse">🎰</div>

          <h1 className="text-3xl font-black text-yellow-400 tracking-wide">
            goldenspinblack.com
          </h1>

          <p className="text-zinc-300 text-sm mt-2 max-w-sm mx-auto leading-relaxed">
            Ganhe giros, desbloqueie bônus e receba recompensas instantâneas via PIX.
          </p>
        </div>

        <div className="p-4 space-y-4">
          <div className="bg-zinc-900 border border-yellow-500/10 rounded-[30px] p-4 shadow-2xl">
            <div className="relative w-full max-w-[300px] mx-auto aspect-square mb-4">
              <div className="absolute -top-4 left-1/2 -translate-x-1/2 text-red-500 text-5xl z-30">
                ▼
              </div>

              <div
                className="w-full h-full rounded-full border-[8px] border-yellow-400 transition-transform duration-[4000ms] ease-out shadow-[0_0_40px_rgba(255,215,0,0.25)]"
                style={{
                  transform: `rotate(${angulo}deg)`,
                  background:
                    'conic-gradient(#dc2626 0deg 45deg,#18181b 45deg 90deg,#16a34a 90deg 135deg,#dc2626 135deg 180deg,#18181b 180deg 225deg,#dc2626 225deg 270deg,#16a34a 270deg 315deg,#18181b 315deg 360deg)',
                }}
              >
                <>
                  

                  <div className="absolute inset-0 flex items-center justify-center">
                    <div className="w-20 h-20 rounded-full bg-black border-4 border-yellow-400 flex items-center justify-center text-yellow-400 font-black text-lg shadow-xl">
                      GIRAR
                    </div>
                  </div>
                </>
              </div>
            </div>

            <div className="bg-black border border-zinc-800 rounded-2xl p-3 text-center mb-3 min-h-[60px] flex items-center justify-center">
              <p className="text-sm font-bold text-yellow-300">{resultado}</p>
            </div>

            <button
              onClick={girarRoleta}
              disabled={girando || girosRestantes <= 0}
              className="w-full bg-gradient-to-r from-yellow-300 to-yellow-500 text-black py-3 rounded-2xl font-black text-sm shadow-[0_10px_25px_rgba(250,204,21,0.3)] active:scale-95 transition-all disabled:opacity-50"
            >
              {girando ? 'Girando...' : '🎯 Girar Agora'}
            </button>
          </div>

          <div className="grid grid-cols-2 gap-3">
            <div className="bg-zinc-900 border border-zinc-800 rounded-2xl p-3 text-center">
              <p className="text-zinc-400 text-xs">Saldo</p>
              <h3 className="text-green-400 font-black text-lg">R$ {saldo}</h3>
            </div>

            <div className="bg-zinc-900 border border-zinc-800 rounded-2xl p-3 text-center">
              <p className="text-zinc-400 text-xs">Giros</p>
              <h3 className="text-blue-400 font-black text-lg">{girosRestantes}</h3>
            </div>
          </div>

          <div className="bg-zinc-900 border border-yellow-500/10 rounded-[30px] p-4 space-y-3">
            <button
              onClick={gerarPix}
              className="w-full bg-gradient-to-r from-green-400 to-green-600 text-black py-3 rounded-2xl font-black text-sm active:scale-95 transition-all"
            >
              Depositar 💸
            </button>

            {pixGerado && (
              <div className="bg-black border border-zinc-800 rounded-2xl p-3 text-center">
                <p className="text-yellow-400 font-bold text-sm mb-2">
                  PIX Copia e Cola
                </p>

                <div className="bg-zinc-900 rounded-xl p-2 text-xs break-all text-zinc-300 mb-3">
                  18884375614
                </div>

                <button
                  onClick={copiarPix}
                  className="w-full bg-yellow-400 text-black py-2 rounded-xl font-black text-xs"
                >
                  Copiar Chave PIX
                </button>

                <p className="text-red-400 text-xs mt-3">
                  Expira em: {formatarTempo(tempoRestante)}
                </p>
              </div>
            )}
          </div>

          <div className="bg-zinc-900 border border-yellow-500/10 rounded-[30px] p-4 space-y-3">
            <h2 className="text-yellow-400 font-black text-center text-sm">
              Saque via PIX 💰
            </h2>

            <div className="bg-black border border-zinc-800 rounded-2xl p-3 flex justify-between items-center text-sm">
              <span>Valor do saque</span>
              <span className="text-green-400 font-black">R$ 30,00</span>
            </div>

            <input
              type="text"
              value={nomeSaque}
              onChange={(e) => setNomeSaque(e.target.value)}
              placeholder="Seu nome completo"
              className="w-full bg-black border border-zinc-800 rounded-2xl px-4 py-3 text-sm outline-none"
            />

            <select
              value={tipoPix}
              onChange={(e) => setTipoPix(e.target.value)}
              className="w-full bg-black border border-zinc-800 rounded-2xl px-4 py-3 text-sm outline-none"
            >
              <option>Tipo de chave PIX</option>
              <option>CPF</option>
              <option>E-mail</option>
              <option>Telefone</option>
              <option>Chave Aleatória</option>
              <option>CNPJ</option>
            </select>

            <input
              type="text"
              value={chavePix}
              onChange={(e) => setChavePix(e.target.value)}
              placeholder="Digite sua chave PIX"
              className="w-full bg-black border border-zinc-800 rounded-2xl px-4 py-3 text-sm outline-none"
            />

            <button
              onClick={handleSaque}
              disabled={!nomeSaque || !tipoPix || !chavePix}
              className="w-full bg-gradient-to-r from-yellow-300 to-yellow-500 disabled:opacity-50 disabled:cursor-not-allowed text-black py-3 rounded-2xl font-black text-sm"
            >
              Sacar R$ 30,00 💵
            </button>

            {resultado === '⚠ Saldo insuficiente' && (
              <div className="bg-red-500/10 border border-red-500/20 rounded-2xl p-3 text-center">
                <p className="text-red-400 font-bold text-sm">
                  ⚠ Saldo insuficiente
                </p>

                <p className="text-zinc-400 text-xs mt-1">
                  Você precisa atingir o valor mínimo de R$ 30,00.
                </p>
              </div>
            )}

            <div className="bg-green-500/10 border border-green-500/20 rounded-2xl p-3 text-center">
              <p className="text-green-400 font-bold text-sm">
                ⚡ PIX instantâneo
              </p>

              <p className="text-zinc-400 text-xs mt-1">
                Após a confirmação, o valor pode cair rapidamente na sua conta.
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}
