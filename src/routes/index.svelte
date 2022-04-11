<script lang="ts">
  const cableType = ['IEC01', 'CV-FD', 'custom']
  const cableArr = {
    IEC01: {
      sustainedCur: 69,
      maxTemp: 70,
      r20: 1.83e-3,
      constant: 0.05,
      axialSpacing: 0.0053,
      crossSection: 10
    },
    'CV-FD': {
      sustainedCur: 69,
      maxTemp: 90,
      r20: 3.08e-3,
      constant: 0.05,
      axialSpacing: 0.0079,
      crossSection: 6
    }
  }

  let sysVoltage = 220
  let sysFreq = 50
  let cableDesignCur = 32
  let cableLength = 20
  let ambientTemp = 40
  let a20 = 3.93e-3
  let cableSelect = 'IEC01'
  let inputDisabled = true
  let sustainedCur = cableArr[cableSelect].sustainedCur
  let maxTemp = cableArr[cableSelect].maxTemp
  let r20 = cableArr[cableSelect].r20
  let constant = cableArr[cableSelect].constant
  let axialSpacing = cableArr[cableSelect].axialSpacing
  let crossSection = cableArr[cableSelect].crossSection

  $: diameter = Math.sqrt((crossSection / Math.PI) * 4) / 1000
  $: cableInduct = 1e-6 * (constant + 0.2 * Math.log((2 * axialSpacing) / diameter))
  $: cableReact = 2 * Math.PI * sysFreq * cableInduct
  $: conductOperTemp = (cableDesignCur / sustainedCur) * (maxTemp - ambientTemp) + ambientTemp
  $: tempAdjResist = r20 * (1 + a20 * (conductOperTemp - 20))

  let threePhase = 1
  $: voltDrop =
    (100 * threePhase * (tempAdjResist * 0.8 + cableReact * 0.6) * cableDesignCur * cableLength) /
    sysVoltage
  let singlePhase = 1
  $: powerLoss = Math.pow(cableDesignCur, 2 * tempAdjResist * singlePhase * cableLength)

  let slopeFactor = -0.0693
  let interceptFactor = 18.5678
  $: lifeHrs = Math.pow(Math.exp(1), slopeFactor * conductOperTemp + interceptFactor)
  $: lifeYrs = lifeHrs / (24 * 365)

  const handleCableType = () => {
    if (cableSelect !== 'custom') {
      sustainedCur = cableArr[cableSelect].sustainedCur
      maxTemp = cableArr[cableSelect].maxTemp
      r20 = cableArr[cableSelect].r20
      constant = cableArr[cableSelect].constant
      axialSpacing = cableArr[cableSelect].axialSpacing
      crossSection = cableArr[cableSelect].crossSection
      inputDisabled = true
    } else {
      inputDisabled = false
    }
  }
</script>

<div class="w-full h-screen flex flex-col gap-6 justify-center items-center bg-white dark:bg-black">
  <h1 class="text-2xl sm:text-4xl dark:text-teal-200 text-teal-800 text-center">
    <span>Cable Operating Temperature Calculator</span>
  </h1>
  <form class="w-11/12 sm:w-5/6 grid grid-cols-2 gap-2 sm:gap-6 justify-center items-center">
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="sysVoltage">
        System Voltage (V)
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter System Voltage"
        name="sysVoltage"
        bind:value={sysVoltage}
      />
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="sysFreq">
        Frequency (Hz)
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter System Frequency"
        name="sysFreq"
        bind:value={sysFreq}
      />
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="cableDesignCur">
        Cable Design Current (A)
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter Cable Design Current"
        name="cableDesignCur"
        bind:value={cableDesignCur}
      />
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="cableLength">
        Cable Length (m)
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter Cable Length"
        name="cableLength"
        bind:value={cableLength}
      />
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="ambientTemp">
        Ambient Temp (°C)
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter Ambient Temp"
        name="ambientTemp"
        bind:value={ambientTemp}
      />
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="a20">
        Temperature Coefficient
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter Ambient Temp"
        name="a20"
        bind:value={a20}
      />
    </div>
    <div class="col-span-2 w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="wireType">
        Wire Type
      </label>
      <select
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        name="wireType"
        bind:value={cableSelect}
        on:change={handleCableType}
      >
        {#each cableType as cable}
          <option value={cable}>{cable}</option>
        {/each}
      </select>
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="sustainedCur">
        Sustained Current Capacity (A)
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter Wire Sustained Current Capacity"
        name="sustainedCur"
        bind:value={sustainedCur}
        disabled={inputDisabled}
      />
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="maxTemp">
        Maximum Operating Temperature (°C)
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter Wire Maximum Operating Temperature"
        name="maxTemp"
        bind:value={maxTemp}
        disabled={inputDisabled}
      />
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="r20">
        Resistance of the cable at 20 °C (Ω/m)
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter Wire Resistance at 20 °C"
        name="r20"
        bind:value={r20}
        disabled={inputDisabled}
      />
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="constant">
        Conductor Formation Constant
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter Wire Conductor Formation Constant"
        name="constant"
        bind:value={constant}
        disabled={inputDisabled}
      />
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="axialSpacing">
        Axial Spacing
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter Wire Axial Spacing"
        name="axialSpacing"
        bind:value={axialSpacing}
        disabled={inputDisabled}
      />
    </div>
    <div class="w-full flex flex-row gap-2 sm:gap-6 justify-center items-center">
      <label class="text-sm sm:text-base dark:text-teal-200 text-teal-800" for="crossSection">
        Cross Sectional Area (mm²)
      </label>
      <input
        type="text"
        class="text-sm sm:text-base grow w-20 sm:w-fit p-2 rounded-lg dark:bg-teal-200 dark:text-teal-800 bg-teal-800 text-teal-100"
        placeholder="Enter Wire Cross Sectional Area"
        name="crossSection"
        bind:value={crossSection}
        disabled={inputDisabled}
      />
    </div>
  </form>
  <div
    class="flex flex-col gap-2 sm:gap-4 text-lg sm:text-xl dark:text-teal-200 text-teal-800 text-center"
  >
    <p>Cable {cableSelect} size {crossSection} sq.mm.</p>
    <p>Operating current {cableDesignCur} A with Size {cableLength} m.</p>
    <p>Conductor operating temperature {conductOperTemp.toFixed(2)} °C</p>
    <p>Temperature adjusted resistance {tempAdjResist.toExponential(3)} Ω/m</p>
    <p>Voltage drop {voltDrop.toFixed(2)} %</p>
    <p>Power loss {powerLoss.toFixed(2)} W</p>
    <p>Expected life {lifeHrs.toFixed(2)} hours ({lifeYrs.toFixed(2)} years)</p>
  </div>
</div>
