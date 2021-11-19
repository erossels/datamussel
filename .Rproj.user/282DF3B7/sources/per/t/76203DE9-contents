def ingresos_totales_empresa(empresa = 'Manuelita', ano = 2021):
    import pandas as pd
    import seaborn as sns
    import numpy as np
    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv") 
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)
    idx = pd.IndexSlice

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    # Agrupacion indicadores

    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])

    dict_meses = {
        1:'Ene',
        2:'Feb',
        3:'Mar',
        4:'Abr',
        5:'May',
        6:'Jun',
        7:'Jul',
        8:'Ago',
        9:'Sep',
        10:'Oct',
        11:'Nov',
        12:'Dic',
        2021: '2021'
    }

    pv = ingresos_mmpp[ingresos_mmpp.Empresa == empresa].pivot_table(index=['Tipo','Propiedad'], columns='Mes', values='Valor', aggfunc=np.sum)
    pv = pv[pv.index.get_level_values('Tipo').isin(['MP Bruta', 'MP Neta'])]
    total = pv.sum(axis=1).round(2)
    average = pv.mean(axis=1).round(2)
    pv[13] = total.copy()
    pv[14] = average.copy()

    pv2 = ingresos_mmpp[ingresos_mmpp.Empresa == empresa].pivot_table(index=['Tipo','Propiedad'], columns='Mes', values='Valor', aggfunc=np.sum)
    pv2 = pv2.groupby(level=0).transform(lambda x: (x / x.sum()).round(2))*100
    pv2 = pv2[pv2.index.get_level_values('Tipo').isin(['MP Bruta', 'MP Neta'])]
    total2 = pv2.mean(axis=1).round(2)
    average2 = pv2.mean(axis=1).round(2)
    pv2[13] = total2.copy()
    pv2[14] = average2.copy()

    largo = len(pv2.columns)
    new_index = []
    for i in range(1, largo+1):
        if i == largo - 1:
            new_index.append(('Total','Ton'))
            new_index.append(('Total','%'))
        elif i == largo:
            new_index.append(('Promedio','Ton'))
            new_index.append(('Promedio','%'))
        else: 
            new_index.append((dict_meses[i],'Ton'))
            new_index.append((dict_meses[i],'%'))
    
    pv3 = pd.concat([pv, pv2], axis=1).sort_index(level=0,axis=1)
    pv3.columns = pd.MultiIndex.from_tuples(new_index)

    pv_empresa = pd.concat([
        d.append(d.sum().rename((k, 'Total')))
        for k, d in pv3.groupby(level=0)
    ]).append(pv3.sum().rename(('General', 'Total'))).round()

    largo = len(pv_empresa.columns)
    for i in range(1,largo+1,2):
        if pv_empresa.iloc[0,i]+pv_empresa.iloc[1,i] != 100.0: 
            aux_1 = pv_empresa.iloc[:,[i]].copy()
            aux_2 = pv_empresa.iloc[:,[i-1]].copy()
            pv_empresa.iloc[:,[i-1]] = aux_1.values
            pv_empresa.iloc[:,[i]] = aux_2.values
    
    pv_empresa = pv_empresa.reset_index(level=None, drop=False)
    pv_empresa.Tipo = ["" if pv_empresa.Tipo.iloc[i] == pv_empresa.Tipo.iloc[i-1] else pv_empresa.Tipo.iloc[i] for i in range(len(pv_empresa.Tipo))]

    return pv_empresa
  
def ingresos_totales_industria(ano = 2021):
    import pandas as pd
    import seaborn as sns
    import numpy as np

    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv")
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)

    idx = pd.IndexSlice

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    # Agrupacion indicadores
    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }


    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])

    dict_meses = {
        1:'Ene',
        2:'Feb',
        3:'Mar',
        4:'Abr',
        5:'May',
        6:'Jun',
        7:'Jul',
        8:'Ago',
        9:'Sep',
        10:'Oct',
        11:'Nov',
        12:'Dic',
        2021: '2021'
    }

    pv = ingresos_mmpp.pivot_table(index=['Tipo','Propiedad'], columns='Mes', values='Valor', aggfunc=np.sum)
    pv = pv[pv.index.get_level_values('Tipo').isin(['MP Bruta', 'MP Neta'])]
    total = pv.sum(axis=1).round(2)
    average = pv.mean(axis=1).round(2)
    pv[13] = total.copy()
    pv[14] = average.copy()

    pv2 = ingresos_mmpp.pivot_table(index=['Tipo','Propiedad'], columns='Mes', values='Valor' , aggfunc=np.sum)
    pv2 = pv2.groupby(level=0).transform(lambda x: (x / x.sum()).round(2))*100
    pv2 = pv2[pv2.index.get_level_values('Tipo').isin(['MP Bruta', 'MP Neta'])]
    total2 = pv2.mean(axis=1).round(2)
    average2 = pv2.mean(axis=1).round(2)
    pv2[13] = total2.copy()
    pv2[14] = average2.copy()

    largo = len(pv2.columns)
    new_index = []
    for i in range(1, largo+1):
        if i == largo - 1:
            new_index.append(('Total','Ton'))
            new_index.append(('Total','%'))
        elif i == largo:
            new_index.append(('Promedio','Ton'))
            new_index.append(('Promedio','%'))
        else: 
            new_index.append((dict_meses[i],'Ton'))
            new_index.append((dict_meses[i],'%'))

    pv3 = pd.concat([pv, pv2], axis=1).sort_index(level=0,axis=1)
    pv3.columns = pd.MultiIndex.from_tuples(new_index)


    # Corregir orden de columnas
    largo = len(pv3.columns)-1
    for i in range(1,largo+1,2):
        if pv3.iloc[0,i]+pv3.iloc[1,i] != 100.0: 
            aux_1 = pv3.iloc[:,[i]].copy()
            aux_2 = pv3.iloc[:,[i-1]].copy()
            pv3.iloc[:,[i-1]] = aux_1.values
            pv3.iloc[:,[i]] = aux_2.values

    #Agregar el contador
    contador = ingresos_mmpp.groupby(['Tipo','Propiedad']).Empresa.nunique()
    columnas = [('N Empresas','')]
    contador = contador.to_frame()
    contador.columns = pd.MultiIndex.from_tuples(columnas)
    df_filtro =pv3.merge(contador, how='left',left_index=True, right_index=True)

    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
        aux=0
        for i in range(len(df.index)):
            print(i)
            if df.iloc[i][('N Empresas','')] < thresh:
                df = df.drop(df.index[i-aux], axis=0)
                aux +=1
        return df

    pv3 = remover_filas(df_filtro)

    pv_todas = pd.concat([
        d.append(d.sum().rename((k, 'Total')))
        for k, d in pv3.groupby(level=0)
    ]).append(pv3.sum().rename(('General', 'Total'))).round()

    pv_todas = pv_todas.reset_index(level=None, drop=False)
    pv_todas.Tipo = ["" if pv_todas.Tipo.iloc[i] == pv_todas.Tipo.iloc[i-1] else pv_todas.Tipo.iloc[i] for i in range(len(pv_todas.Tipo))]
    
    return pv_todas

def ingresos_totales_otras(empresa = 'Manuelita',ano = 2021):
    import pandas as pd
    import seaborn as sns
    import numpy as np

    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv")
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)
    idx = pd.IndexSlice

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    # Agrupacion indicadores

    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])

    dict_meses = {
        1:'Ene',
        2:'Feb',
        3:'Mar',
        4:'Abr',
        5:'May',
        6:'Jun',
        7:'Jul',
        8:'Ago',
        9:'Sep',
        10:'Oct',
        11:'Nov',
        12:'Dic',
        2021: '2021'
    }

    pv = ingresos_mmpp[ingresos_mmpp.Empresa != empresa].pivot_table(index=['Tipo','Propiedad'], columns='Mes', values='Valor', aggfunc=np.sum)
    pv = pv[pv.index.get_level_values('Tipo').isin(['MP Bruta', 'MP Neta'])]
    total = pv.sum(axis=1).round(2)
    average = pv.mean(axis=1).round(2)
    pv[13] = total.copy()
    pv[14] = average.copy()

    pv2 = ingresos_mmpp[ingresos_mmpp.Empresa != empresa].pivot_table(index=['Tipo','Propiedad'], columns='Mes', values='Valor', aggfunc=np.sum)
    pv2 = pv2.groupby(level=0).transform(lambda x: (x / x.sum()).round(2))*100
    pv2 = pv2[pv2.index.get_level_values('Tipo').isin(['MP Bruta', 'MP Neta'])]
    total2 = pv2.mean(axis=1).round(2)
    average2 = pv2.mean(axis=1).round(2)
    pv2[13] = total2
    pv2[14] = average2

    largo = len(pv2.columns)
    new_index = []
    for i in range(1, largo+1):
        if i == largo - 1:
            new_index.append(('Total','Ton'))
            new_index.append(('Total','%'))
        elif i == largo:
            new_index.append(('Promedio','Ton'))
            new_index.append(('Promedio','%'))
        else: 
            new_index.append((dict_meses[i],'Ton'))
            new_index.append((dict_meses[i],'%'))

    pv3 = pd.concat([pv, pv2], axis=1).sort_index(level=0,axis=1)
    pv3.columns = pd.MultiIndex.from_tuples(new_index)

    #Agregar el contador
    contador = ingresos_mmpp.groupby(['Tipo','Propiedad']).Empresa.nunique()
    columnas = [('N Empresas','')]
    contador = contador.to_frame()
    contador.columns = pd.MultiIndex.from_tuples(columnas)
    df_filtro =pv3.merge(contador, how='left',left_index=True, right_index=True)

    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
        for i in range(len(df)):
            if df.iloc[i][('N Empresas','')] <= thresh:
                df = df.drop([i], axis=0)
        return df

    pv3 = remover_filas(df_filtro)

    pv_otras = pd.concat([
        d.append(d.sum().rename((k, 'Total')))
        for k, d in pv3.groupby(level=0)
    ]).append(pv3.sum().rename(('General', 'Total'))).round()

    largo = len(pv_otras.columns)-1
    for i in range(1,largo+1,2):
        if pv_otras.iloc[0,i]+pv_otras.iloc[1,i] != 100.0: 
            aux_1 = pv_otras.iloc[:,[i]].copy()
            aux_2 = pv_otras.iloc[:,[i-1]].copy()
            print(aux_1)
            print(aux_2)
            pv_otras.iloc[:,[i-1]] = aux_1.values
            pv_otras.iloc[:,[i]] = aux_2.values
    

    pv_otras = pv_otras.reset_index(level=None, drop=False)
    pv_otras.Tipo = ["" if pv_otras.Tipo.iloc[i] == pv_otras.Tipo.iloc[i-1] else pv_otras.Tipo.iloc[i] for i in range(len(pv_otras.Tipo))]
        
    return pv_otras

def ingresos_acumulado(empresa = 'Manuelita',ano = 2021):
    import pandas as pd
    import numpy as np
    
    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv") 
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)
    
    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    idx = pd.IndexSlice
    
    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
        aux=0
        for i in range(len(df.index)):
            print(i)
            if df.iloc[i][('N Empresas','')] < thresh:
                df = df.drop(df.index[i-aux], axis=0)
                aux +=1
        return df
    
    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])

    # Acumulado ano INDUSTRIA

    dict_propiedad = {
    'Ingreso Materia Bruta Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta o Pagada Propia':'Materia Prima Propia',
    'Unidades por kilo mmpp propia*':'Materia Prima Propia',
    'Ingreso Materia Prima Bruta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Materia Prima Terceros',
    'Unidades por kilo mmpp terceros*':'Materia Prima Terceros',
    'Ingreso Materia Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Bruta Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Terceros':'Materia Prima Terceros',
    '% Rechazo Materia Prima Total':'Total Materia Prima',
    'rechazo propio': 'Materia Prima Propia',
    'rechazo terceros': 'Materia Prima Terceros'
    }

    dict_indicadores_2 = {
    'Ingreso Materia Bruta Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Propia':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp propia*':'Unidades por kilo',
    'Ingreso Materia Prima Bruta Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp terceros*':'Unidades por kilo',
    'Ingreso Materia Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Propia':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Terceros':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Bruta Propia':'Ingreso Materia Prima Bruta',
    '% Rechazo Materia Prima Propia':'% Rechazo MMPP',
    '% Rechazo Materia Prima Terceros':'% Rechazo MMPP',
    '% Rechazo Materia Prima Total':'% Rechazo MMPP',
    'rechazo propio': "% Rechazo MMPP",
    'rechazo terceros': "% Rechazo MMPP"
    }

    #Lectura datos y creacion nuevas columnas
    df = ingresos_mmpp.copy()
    df['Indicadores'] = [dict_indicadores_2[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]
    df['Propietario'] = [dict_propiedad[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]
    df_aux = df.copy()

    # Creacion columnas con Total Materia Prima
    new_df = pd.DataFrame(columns = df.columns)
    for e in df.Empresa.unique():
        for m in df.Mes.unique():
            new_row = {
                'Indicador':'Ingreso Materia Prima Bruta', 
                'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Bruta')].Valor.sum(),
                'Unidad de medida':'Ton', 
                'Definicion':"definicion", 
                'Mes':m, 
                'Ano':2021,
                'Empresa':e,
                'Tipo':'MP Bruta', 
                'Propiedad':'', 
                'Indicadores':'Ingreso Materia Prima Bruta', 
                'Propietario':'Total Materia Prima'
            }
            new_df = new_df.append(new_row, ignore_index = True)
            
            new_row = {
                'Indicador':'Ingreso Materia Prima Neta', 
                'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Neta')].Valor.sum(),
                'Unidad de medida':'Ton', 
                'Definicion': "definicion", 
                'Mes':m, 
                'Ano':2021,
                'Empresa':e,
                'Tipo':'MP Bruta', 
                'Propiedad':'', 
                'Indicadores':'Ingreso Materia Prima Neta', 
                'Propietario':'Total Materia Prima'
            }
            new_df = new_df.append(new_row, ignore_index = True)

    df = df.append(new_df, ignore_index=True)
    df = df.groupby(['Propietario','Indicadores']).Valor.agg(['sum', np.mean], axis='columns')
    df = df.round(2)
    df.columns = ['Total', 'Promedio']
    
    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas_single(df, thresh=3):
        aux = 0
        for i in range(len(df)):
            if (df.iloc[i-aux]['N Empresas'] < thresh) & (df.iloc[i-aux]['N Empresas'] != np.nan):
                df = df.drop(df.index[i-aux])
                aux +=1
        return df
    
    #Agregar el contador
    contador = df_aux.groupby(['Propietario','Indicadores']).Empresa.nunique()
    try:
      columnas = [('N Empresas','')]
      contador = contador.to_frame()
      contador.columns = pd.MultiIndex.from_tuples(columnas)
      df_filtro =df.merge(contador, how='left',left_index=True, right_index=True)
      # Funcion para remover filas con indicadores para menos de 3 empresas
      df = remover_filas(df_filtro)
    except:
      columnas = ['N Empresas']
      contador.columns = columnas
      df_filtro =df.merge(contador, how='left',left_index=True, right_index=True)
      # Funcion para remover filas con indicadores para menos de 3 empresas
      df = remover_filas_single(df_filtro)


    # Promedio ponderado unidades por kilo
    for i, j in df.index:
        df.loc[(i,'% Rechazo mmpp'), 'Total'] = (df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']-df.loc[(i,'Ingreso Materia Prima Neta'), 'Total'])*100/df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']
        if i != 'Total Materia Prima':
            df.loc[(i,'% MP Bruta Propia sobre Total MP Bruta'),'Total'] = df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']/df.loc[('Total Materia Prima','Ingreso Materia Prima Bruta'),'Total']*100
        if (i == 'Materia Prima Propia') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
            uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_propia    
            
        if (i == 'Materia Prima Terceros') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
            uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_terceros

    df = df.round(2).sort_index(ascending = [True, False])

    #Corregir orden de filas
    try:
        df = df.loc[[('Materia Prima Propia', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Propia', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Propia', 'Unidades por kilo'),
        ('Materia Prima Propia', '% Rechazo mmpp'),
        ('Materia Prima Propia', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Terceros', 'Unidades por kilo'),
        ('Materia Prima Terceros', '% Rechazo mmpp'),
        ('Materia Prima Terceros', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Neta'),
        ('Total Materia Prima', '% Rechazo mmpp'),
        ],:].fillna(0)
    except:
        df.fillna(0)
    
    #Aplanar tabla para visualizar con R

    df_industrias = df.copy()

    df = df.reset_index(level=None, drop=False).fillna(0)
    df.Propietario = ["" if df.Propietario.iloc[i] == df.Propietario.iloc[i-1] else df.Propietario.iloc[i] for i in range(len(df.Propietario))]


    # ACUMULADO AÑO EMPRESA

    # Nuevas columnas
    df = ingresos_mmpp[ingresos_mmpp.Empresa == empresa].copy()
    df['Indicadores'] = [dict_indicadores_2[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]
    df['Propietario'] = [dict_propiedad[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]

    # Añadir nuevas columnas para el total
    new_df = pd.DataFrame(columns = df.columns)

    for e in df.Empresa.unique():
        for m in df.Mes.unique():
            new_row = {
                'Indicador':'Ingreso Materia Prima Bruta', 
                'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Bruta')].Valor.sum(),
                'Unidad de medida':'Ton', 
                'Definicion':'', 
                'Mes':m, 
                'Ano':2021,
                'Empresa':e,
                'Tipo':'MP Bruta', 
                'Propiedad':'', 
                'Indicadores':'Ingreso Materia Prima Bruta', 
                'Propietario':'Total Materia Prima'
            }
            new_df = new_df.append(new_row, ignore_index = True)
            
            new_row = {
                'Indicador':'Ingreso Materia Prima Neta', 
                'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Neta')].Valor.sum(),
                'Unidad de medida':'Ton', 
                'Definicion':'', 
                'Mes':m, 
                'Ano':2021,
                'Empresa':e,
                'Tipo':'MP Bruta', 
                'Propiedad':'', 
                'Indicadores':'Ingreso Materia Prima Neta', 
                'Propietario':'Total Materia Prima'
            }
            new_df = new_df.append(new_row, ignore_index = True)
            
    df = df.append(new_df, ignore_index=True)
    df = df.groupby(['Propietario','Indicadores']).Valor.agg(['sum', np.mean], axis='columns')
    df = df.round(2)
    df.columns = ['Total', 'Promedio']

    # Promedio ponderado unidades por kilo
    df2 = ingresos_mmpp[ingresos_mmpp.Empresa == empresa].copy()
    df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
    df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
    filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')

    df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)

    df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
    uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()


    df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
    uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()


    #Cambios en Unidades por Kilo y %MP Bruta

    for i, j in df.index:
        df.loc[(i,'% Rechazo mmpp'), 'Total'] = (df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']-df.loc[(i,'Ingreso Materia Prima Neta'), 'Total'])*100/df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']
        if i != 'Total Materia Prima':
            df.loc[(i,'% MP Bruta Propia sobre Total MP Bruta'),'Total'] = df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']/df.loc[('Total Materia Prima','Ingreso Materia Prima Bruta'),'Total']*100
        if (i == 'Materia Prima Propia') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
            uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_propia    
            
        if (i == 'Materia Prima Terceros') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
            uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_terceros

    df.round(2).sort_index(ascending = [True, False])

    try:
        df = df.loc[[('Materia Prima Propia', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Propia', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Propia', 'Unidades por kilo'),
        ('Materia Prima Propia', '% Rechazo mmpp'),
        ('Materia Prima Propia', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Terceros', 'Unidades por kilo'),
        ('Materia Prima Terceros', '% Rechazo mmpp'),
        ('Materia Prima Terceros', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Neta'),
        ('Total Materia Prima', '% Rechazo mmpp'),
        ],:].fillna("")
    except:
        df.fillna(0)
    
    df_empresa = df.copy()

    df = df.reset_index(level=None, drop=False).fillna(0)
    df.Propietario = ["" if df.Propietario.iloc[i] == df.Propietario.iloc[i-1] else df.Propietario.iloc[i] for i in range(len(df.Propietario))]


    # ACUMULADO AÑOS OTRAS
    
    df = ingresos_mmpp[ingresos_mmpp.Empresa != empresa].copy()
    df['Indicadores'] = [dict_indicadores_2[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]
    df['Propietario'] = [dict_propiedad[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]

    df_aux = df.copy()

    # Añadir nuevas columnas para el total
    new_df = pd.DataFrame(columns = df.columns)

    for e in df.Empresa.unique():
        for m in df.Mes.unique():
            new_row = {
                'Indicador':'Ingreso Materia Prima Bruta', 
                'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Bruta')].Valor.sum(),
                'Unidad de medida':'Ton', 
                'Definicion':'', 
                'Mes':m, 
                'Ano':2021,
                'Empresa':e,
                'Tipo':'MP Bruta', 
                'Propiedad':'', 
                'Indicadores':'Ingreso Materia Prima Bruta', 
                'Propietario':'Total Materia Prima'
            }
            new_df = new_df.append(new_row, ignore_index = True)
            
            new_row = {
                'Indicador':'Ingreso Materia Prima Neta', 
                'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Neta')].Valor.sum(),
                'Unidad de medida':'Ton', 
                'Definicion':'', 
                'Mes':m, 
                'Ano':2021,
                'Empresa':e,
                'Tipo':'MP Bruta', 
                'Propiedad':'', 
                'Indicadores':'Ingreso Materia Prima Neta', 
                'Propietario':'Total Materia Prima'
            }
            new_df = new_df.append(new_row, ignore_index = True)
            
    df = df.append(new_df, ignore_index=True)
    df = df.groupby(['Propietario','Indicadores']).Valor.agg(['sum', np.mean], axis='columns')
    df = df.round(2)
    df.columns = ['Total', 'Promedio']

    #Agregar el contador
    contador = df_aux.groupby(['Propietario','Indicadores']).Empresa.nunique()
    try:
      columnas = [('N Empresas','')]
      contador = contador.to_frame()
      contador.columns = pd.MultiIndex.from_tuples(columnas)
      df_filtro =df.merge(contador, how='left',left_index=True, right_index=True)
      # Funcion para remover filas con indicadores para menos de 3 empresas
      df = remover_filas(df_filtro)
    except:
      columnas = ['N Empresas']
      contador.columns = columnas
      df_filtro =df.merge(contador, how='left',left_index=True, right_index=True)
      # Funcion para remover filas con indicadores para menos de 3 empresas
      df = remover_filas_single(df_filtro)
      

    # Promedio ponderado unidades por kilo
    df2 = ingresos_mmpp[ingresos_mmpp.Empresa != empresa].copy()
    df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
    df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
    filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')

    df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)

    df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
    uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()


    df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
    uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()


    #Cambios en Unidades por Kilo y %MP Bruta

    for i, j in df.index:
        df.loc[(i,'% Rechazo mmpp'), 'Total'] = (df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']-df.loc[(i,'Ingreso Materia Prima Neta'), 'Total'])*100/df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']
        if i != 'Total Materia Prima':
            df.loc[(i,'% MP Bruta Propia sobre Total MP Bruta'),'Total'] = df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']/df.loc[('Total Materia Prima','Ingreso Materia Prima Bruta'),'Total']*100
        if (i == 'Materia Prima Propia') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
            uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_propia    
            
        if (i == 'Materia Prima Terceros') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
            uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_terceros

    df.round(2).sort_index(ascending = [True, False])

    try:
        df = df.loc[[('Materia Prima Propia', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Propia', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Propia', 'Unidades por kilo'),
        ('Materia Prima Propia', '% Rechazo mmpp'),
        ('Materia Prima Propia', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Terceros', 'Unidades por kilo'),
        ('Materia Prima Terceros', '% Rechazo mmpp'),
        ('Materia Prima Terceros', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Neta'),
        ('Total Materia Prima', '% Rechazo mmpp'),
        ],:].fillna("")
    except:
        df.fillna("")

    df.round(2)

    df_otras = df.copy()

    df = df.reset_index(level=None, drop=False).fillna(0)
    df.Propietario = ["" if df.Propietario.iloc[i] == df.Propietario.iloc[i-1] else df.Propietario.iloc[i] for i in range(len(df.Propietario))]

    # JUNTAR TODAS LOS DFs

    df_all = df_industrias.merge(df_empresa, how='outer', on=['Propietario','Indicadores'])
    df_all = df_all.merge(df_otras, how='outer',on=['Propietario','Indicadores'])
    df_all.sort_index(ascending = [True, False])

    try:
        df_all = df_all.loc[[('Materia Prima Propia', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Propia', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Propia', 'Unidades por kilo'),
        ('Materia Prima Propia', '% Rechazo mmpp'),
        ('Materia Prima Propia', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Terceros', 'Unidades por kilo'),
        ('Materia Prima Terceros', '% Rechazo mmpp'),
        ('Materia Prima Terceros', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Neta'),
        ('Total Materia Prima', '% Rechazo mmpp'),
        ],:].fillna("")
    except:
        df_all.fillna(0)
    
    
    df_all = df_all.reset_index(level=None, drop=False).fillna(0)
    df_all.Propietario = ["" if df_all.Propietario.iloc[i] == df_all.Propietario.iloc[i-1] else df_all.Propietario.iloc[i] for i in range(len(df_all.Propietario))]
    try:
      df_all.drop(["('N Empresas', '')_x"], axis=1, inplace=True)
    except:
      df_all.drop(['N Empresas'], axis=1, inplace=True)

    return df_all

def ingresos_mes(empresa = 'Manuelita', mes = 'Ene',ano = 2021):
    import pandas as pd
    import numpy as np
          # Cambiar a numero
    dict_mes_num = {
        'Ene':1,
          'Feb':2,
          'Mar':3,
          'Abr':4,
          'May':5,
          'Jun':6,
          'Jul':7,
          'Ago':8,
          'Sep':9,
          'Oct':10,
          'Nov':11,
          'Dic':12
      }
    mes = dict_mes_num[mes]
  
    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv")
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)
    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)
  
    idx = pd.IndexSlice
      
    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
      aux=0
      for i in range(len(df.index)):
        print(i)
        if df.iloc[i][('N Empresas','')] < thresh:
          df = df.drop(df.index[i-aux], axis=0)
          aux +=1
      return df
      
    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"}
  
    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])
  
    dict_indicadores_propiedad = {
          'Ingreso Materia Bruta Prima Propia':'MPB Propia',
          'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
          'Unidades por kilo mmpp propia*': "No aplica",
          'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
          'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
          'Unidades por kilo mmpp terceros*': "No aplica", 
          'Ingreso Materia Prima Propia':'MPB Propia',
          'Ingreso Materia Prima Neta Propia':'MPN Propia',
          'Ingreso Materia Prima Terceros':'MPB Terceros',
          'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
          'Ingreso Materia Prima Bruta Propia':'MPB Propia',
          'rechazo propio': "No aplica",
          'rechazo terceros': "No aplica"
    }
  
    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])
    
    dict_propiedad = {
    'Ingreso Materia Bruta Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta o Pagada Propia':'Materia Prima Propia',
    'Unidades por kilo mmpp propia*':'Materia Prima Propia',
    'Ingreso Materia Prima Bruta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Materia Prima Terceros',
    'Unidades por kilo mmpp terceros*':'Materia Prima Terceros',
    'Ingreso Materia Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Bruta Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Terceros':'Materia Prima Terceros',
    '% Rechazo Materia Prima Total':'Total Materia Prima',
    'rechazo propio': 'Materia Prima Propia',
    'rechazo terceros': 'Materia Prima Terceros'
    }
    
    dict_indicadores_2 = {
    'Ingreso Materia Bruta Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Propia':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp propia*':'Unidades por kilo',
    'Ingreso Materia Prima Bruta Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp terceros*':'Unidades por kilo',
    'Ingreso Materia Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Propia':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Terceros':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Bruta Propia':'Ingreso Materia Prima Bruta',
    '% Rechazo Materia Prima Propia':'% Rechazo MMPP',
    '% Rechazo Materia Prima Terceros':'% Rechazo MMPP',
    '% Rechazo Materia Prima Total':'% Rechazo MMPP',
    'rechazo propio': "% Rechazo MMPP",
    'rechazo terceros': "% Rechazo MMPP"
    }
 
  
    # Resumen mes. Todas las empresas
    # Nuevas columnas
    df = ingresos_mmpp[ingresos_mmpp.Mes == mes].copy()
    df['Indicadores'] = [dict_indicadores_2[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]
    df['Propietario'] = [dict_propiedad[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]

      #Copia para el proceso de filtrado y conteo de empresas
    df_aux = df.copy()
  
      # Añadir nuevas columnas para el total
    new_df = pd.DataFrame(columns = df.columns)
  
    for e in df.Empresa.unique():
      for m in df.Mes.unique():
        new_row = {
                  'Indicador':'Ingreso Materia Prima Bruta', 
                  'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Bruta')].Valor.sum(),
                  'Unidad de medida':'Ton', 
                  'Definicion':'', 
                  'Mes':m, 
                  'Ano':2021,
                  'Empresa':e,
                  'Tipo':'MP Bruta', 
                  'Propiedad':'', 
                  'Indicadores':'Ingreso Materia Prima Bruta', 
                  'Propietario':'Total Materia Prima'
              }
        
        new_df = new_df.append(new_row, ignore_index = True)
              
        new_row = {
                  'Indicador':'Ingreso Materia Prima Neta', 
                  'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Neta')].Valor.sum(),
                  'Unidad de medida':'Ton', 
                  'Definicion':'', 
                  'Mes':m, 
                  'Ano':2021,
                  'Empresa':e,
                  'Tipo':'MP Bruta', 
                  'Propiedad':'', 
                  'Indicadores':'Ingreso Materia Prima Neta', 
                  'Propietario':'Total Materia Prima'
              }
        new_df = new_df.append(new_row, ignore_index = True)
              
    df = df.append(new_df, ignore_index=True)
    df = df.groupby(['Propietario','Indicadores']).Valor.agg(['sum', np.mean], axis='columns')
    df = df.round(2)
    df.columns = ['Total', 'Promedio']

    #Agregar el contador
    contador = df_aux.groupby(['Propietario','Indicadores']).Empresa.nunique()
    columnas = 'N Empresas'
    contador = contador.to_frame()
    contador.columns = [columnas]
    df_filtro =df.merge(contador, how='left',left_index=True, right_index=True)

    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas_single(df, thresh=3):
        aux = 0
        for i in range(len(df)):
            if (df.iloc[i-aux]['N Empresas'] < thresh) & (df.iloc[i-aux]['N Empresas'] != np.nan):
                df = df.drop(df.index[i-aux])
                aux +=1
        return df

    df = remover_filas_single(df_filtro)

    # Promedio ponderado unidades por kilo
    df2 = ingresos_mmpp[ingresos_mmpp.Mes == mes].copy()
    df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
    df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
    filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')

    df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)

    df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
    uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()


    df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
    uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()

  
    #Cambios en Unidades por Kilo y %MP Bruta

    for i, j in df.index:
        df.loc[(i,'% Rechazo mmpp'), 'Total'] = (df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']-df.loc[(i,'Ingreso Materia Prima Neta'), 'Total'])*100/df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']
        if i != 'Total Materia Prima':
            df.loc[(i,'% MP Bruta Propia sobre Total MP Bruta'),'Total'] = df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']/df.loc[('Total Materia Prima','Ingreso Materia Prima Bruta'),'Total']*100
        if (i == 'Materia Prima Propia') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
            uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_propia    
            
        if (i == 'Materia Prima Terceros') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
            uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_terceros

    df.round(2).sort_index(ascending = [True, False])

    try:
        df = df.loc[[('Materia Prima Propia', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Propia', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Propia', 'Unidades por kilo'),
        ('Materia Prima Propia', '% Rechazo mmpp'),
        ('Materia Prima Propia', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Terceros', 'Unidades por kilo'),
        ('Materia Prima Terceros', '% Rechazo mmpp'),
        ('Materia Prima Terceros', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Neta'),
        ('Total Materia Prima', '% Rechazo mmpp'),
        ],:].fillna("")
    except:
        df.fillna("")

    df_industrias_mes = df.copy()

    #### INGRESOS MES EMPRESA

    # Nuevas columnas
    df = ingresos_mmpp[(ingresos_mmpp.Empresa == empresa) & (ingresos_mmpp.Mes == mes)].copy()
    df['Indicadores'] = [dict_indicadores_2[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]
    df['Propietario'] = [dict_propiedad[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]
    df.fillna(0)

    # Añadir nuevas columnas para el total
    new_df = pd.DataFrame(columns = df.columns)

    for e in df.Empresa.unique():
        for m in df.Mes.unique():
            new_row = {
                'Indicador':'Ingreso Materia Prima Bruta', 
                'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Bruta')].Valor.sum(),
                'Unidad de medida':'Ton', 
                'Definicion':'', 
                'Mes':m, 
                'Ano':2021,
                'Empresa':e,
                'Tipo':'MP Bruta', 
                'Propiedad':'', 
                'Indicadores':'Ingreso Materia Prima Bruta', 
                'Propietario':'Total Materia Prima'
            }
            new_df = new_df.append(new_row, ignore_index = True)
            
            new_row = {
                'Indicador':'Ingreso Materia Prima Neta', 
                'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Neta')].Valor.sum(),
                'Unidad de medida':'Ton', 
                'Definicion':'', 
                'Mes':m, 
                'Ano':2021,
                'Empresa':e,
                'Tipo':'MP Bruta', 
                'Propiedad':'', 
                'Indicadores':'Ingreso Materia Prima Neta', 
                'Propietario':'Total Materia Prima'
            }
            new_df = new_df.append(new_row, ignore_index = True)
            
    df = df.append(new_df, ignore_index=True)
    df = df.groupby(['Propietario','Indicadores']).Valor.agg(['sum', np.mean], axis='columns')
    df = df.round(2)
    df.columns = ['Total', 'Promedio']

    # Promedio ponderado unidades por kilo
    df2 = ingresos_mmpp[(ingresos_mmpp.Empresa == empresa) & (ingresos_mmpp.Mes == mes)].copy()
    df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
    df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
    filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')

    df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)

    df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
    uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()


    df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
    uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()


    #Cambios en Unidades por Kilo y %MP Bruta

    for i, j in df.index:
        df.loc[(i,'% Rechazo mmpp'), 'Total'] = (df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']-df.loc[(i,'Ingreso Materia Prima Neta'), 'Total'])*100/df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']
        if i != 'Total Materia Prima':
            df.loc[(i,'% MP Bruta Propia sobre Total MP Bruta'),'Total'] = df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']/df.loc[('Total Materia Prima','Ingreso Materia Prima Bruta'),'Total']*100
        if (i == 'Materia Prima Propia') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
            uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_propia    
            
        if (i == 'Materia Prima Terceros') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
            uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_terceros

    df.round(2).sort_index(ascending = [True, False])

    try: 
        df = df.loc[[('Materia Prima Propia', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Propia', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Propia', 'Unidades por kilo'),
        ('Materia Prima Propia', '% Rechazo mmpp'),
        ('Materia Prima Propia', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Terceros', 'Unidades por kilo'),
        ('Materia Prima Terceros', '% Rechazo mmpp'),
        ('Materia Prima Terceros', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Neta'),
        ('Total Materia Prima', '% Rechazo mmpp'),
        ],:].fillna("")
    except:
        print('')
    finally: 
        df = df.round(2).sort_index(ascending = [True, False])
        
    df_empresa_mes = df.copy()

    #### INGRESOS MES OTRAS EMPRESAS ######################################
        
    # Nuevas columnas
    df = ingresos_mmpp[(ingresos_mmpp.Empresa != empresa) & (ingresos_mmpp.Mes == mes)].copy()
    df['Indicadores'] = [dict_indicadores_2[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]
    df['Propietario'] = [dict_propiedad[df.Indicador.iloc[i]] for i in range(len(df.Indicador))]
    df_aux = df.copy()

    # Añadir nuevas columnas para el total
    new_df = pd.DataFrame(columns = df.columns)

    for e in df.Empresa.unique():
        for m in df.Mes.unique():
            new_row = {
                'Indicador':'Ingreso Materia Prima Bruta', 
                'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Bruta')].Valor.sum(),
                'Unidad de medida':'Ton', 
                'Definicion':'', 
                'Mes':m, 
                'Ano':2021,
                'Empresa':e,
                'Tipo':'MP Bruta', 
                'Propiedad':'', 
                'Indicadores':'Ingreso Materia Prima Bruta', 
                'Propietario':'Total Materia Prima'
            }
            new_df = new_df.append(new_row, ignore_index = True)
            
            new_row = {
                'Indicador':'Ingreso Materia Prima Neta', 
                'Valor': df[(df.Empresa == e) & (df.Mes == m) & (df.Tipo == 'MP Neta')].Valor.sum(),
                'Unidad de medida':'Ton', 
                'Definicion':'', 
                'Mes':m, 
                'Ano':2021,
                'Empresa':e,
                'Tipo':'MP Bruta', 
                'Propiedad':'', 
                'Indicadores':'Ingreso Materia Prima Neta', 
                'Propietario':'Total Materia Prima'
            }
            new_df = new_df.append(new_row, ignore_index = True)
            
    df = df.append(new_df, ignore_index=True)
    df = df.groupby(['Propietario','Indicadores']).Valor.agg(['sum', np.mean], axis='columns')
    df = df.round(2)
    df.columns = ['Total', 'Promedio']

    #Agregar el contador
    contador = df_aux.groupby(['Propietario','Indicadores']).Empresa.nunique()
    columnas = 'N Empresas'
    contador = contador.to_frame()
    contador.columns = [columnas]
    df_filtro =df.merge(contador, how='left',left_index=True, right_index=True)

    # Funcion para remover filas con indicadores para menos de 3 empresas
    df = remover_filas_single(df_filtro)


    # Promedio ponderado unidades por kilo
    df2 = ingresos_mmpp[(ingresos_mmpp.Empresa != empresa) & (ingresos_mmpp.Mes == mes)].copy()
    df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
    df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
    filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')

    df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)

    df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
    uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()


    df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
    uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()


    #Cambios en Unidades por Kilo y %MP Bruta

    for i, j in df.index:
        df.loc[(i,'% Rechazo mmpp'), 'Total'] = (df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']-df.loc[(i,'Ingreso Materia Prima Neta'), 'Total'])*100/df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']
        if i != 'Total Materia Prima':
            df.loc[(i,'% MP Bruta Propia sobre Total MP Bruta'),'Total'] = df.loc[(i,'Ingreso Materia Prima Bruta'), 'Total']/df.loc[('Total Materia Prima','Ingreso Materia Prima Bruta'),'Total']*100
        if (i == 'Materia Prima Propia') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_propia = df3.loc[idx[:,:,'Materia Prima Propia'],:]
            uxk_propia = (df3_propia['Ingreso Materia Prima Neta']*df3_propia['Unidades por kilo']).sum()/df3_propia['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_propia    
            
        if (i == 'Materia Prima Terceros') & (j=='Unidades por kilo'):
            # Promedio ponderado unidades por kilo
            df2 = ingresos_mmpp.copy()
            df2['Indicadores'] = [dict_indicadores_2[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            df2['Propietario'] = [dict_propiedad[df2.Indicador.iloc[i]] for i in range(len(df2.Indicador))]
            filtro = (df2.Indicadores == 'Ingreso Materia Prima Neta') | (df2.Indicadores=='Unidades por kilo')
            df3 = df2[filtro].groupby(['Empresa','Mes','Propietario','Indicadores']).Valor.sum().unstack(level=-1).fillna(0)
            df3_terceros = df3.loc[idx[:,:,'Materia Prima Terceros'],:]
            uxk_terceros = (df3_terceros['Ingreso Materia Prima Neta']*df3_terceros['Unidades por kilo']).sum()/df3_terceros['Ingreso Materia Prima Neta'].sum()
            df.loc[(i,j),'Total'] = uxk_terceros

    df.round(2).sort_index(ascending = [True, False])

    try:
        df = df.loc[[('Materia Prima Propia', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Propia', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Propia', 'Unidades por kilo'),
        ('Materia Prima Propia', '% Rechazo mmpp'),
        ('Materia Prima Propia', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Terceros', 'Unidades por kilo'),
        ('Materia Prima Terceros', '% Rechazo mmpp'),
        ('Materia Prima Terceros', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Neta'),
        ('Total Materia Prima', '% Rechazo mmpp'),
        ],:].fillna("")
    except:
        df.fillna("")

    df_otras_mes = df.copy()

    ### JUNTAR EN TABLA RESUMEN

    df_all_mes = df_industrias_mes.merge(df_empresa_mes, how='outer', on=['Propietario','Indicadores'])
    df_all_mes = df_all_mes.merge(df_otras_mes, how='outer',on=['Propietario','Indicadores'])
    df_all_mes.sort_index(ascending = [True, False])

    try:
        df_all_mes = df_all_mes.loc[[('Materia Prima Propia', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Propia', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Propia', 'Unidades por kilo'),
        ('Materia Prima Propia', '% Rechazo mmpp'),
        ('Materia Prima Propia', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Terceros', 'Unidades por kilo'),
        ('Materia Prima Terceros', '% Rechazo mmpp'),
        ('Materia Prima Terceros', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Neta'),
        ('Total Materia Prima', '% Rechazo mmpp'),
        ],:].fillna("")
    except:
        df_all_mes.fillna(0)
    
    
    df_all_mes = df_all_mes.reset_index(level=None, drop=False).fillna(0)
    df_all_mes.Propietario = ["" if df_all_mes.Propietario.iloc[i] == df_all_mes.Propietario.iloc[i-1] else df_all_mes.Propietario.iloc[i] for i in range(len(df_all_mes.Propietario))]
    df_all_mes.drop('N Empresas_x', axis=1, inplace= True)

    return df_all_mes
  
def grafico_MMPP_origen(ano = 2021):
    import pandas as pd
    import numpy as np
    import plotly.express as px
    import plotly.graph_objects as go
    import plotly

    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv") 
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)
    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    idx = pd.IndexSlice
    
    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])

    dict_propiedad = {
    'Ingreso Materia Bruta Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta o Pagada Propia':'Materia Prima Propia',
    'Unidades por kilo mmpp propia*':'Materia Prima Propia',
    'Ingreso Materia Prima Bruta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Materia Prima Terceros',
    'Unidades por kilo mmpp terceros*':'Materia Prima Terceros',
    'Ingreso Materia Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Bruta Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Terceros':'Materia Prima Terceros',
    '% Rechazo Materia Prima Total':'Total Materia Prima',
    'rechazo propio': 'Materia Prima Propia',
    'rechazo terceros': 'Materia Prima Terceros'
    }

    dict_indicadores_2 = {
    'Ingreso Materia Bruta Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Propia':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp propia*':'Unidades por kilo',
    'Ingreso Materia Prima Bruta Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp terceros*':'Unidades por kilo',
    'Ingreso Materia Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Propia':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Terceros':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Bruta Propia':'Ingreso Materia Prima Bruta',
    '% Rechazo Materia Prima Propia':'% Rechazo MMPP',
    '% Rechazo Materia Prima Terceros':'% Rechazo MMPP',
    '% Rechazo Materia Prima Total':'% Rechazo MMPP',
    'rechazo propio': "% Rechazo MMPP",
    'rechazo terceros': "% Rechazo MMPP"
    }


    datos = ingresos_mmpp[(ingresos_mmpp.Tipo== 'MP Bruta')].copy()[['Empresa','Propiedad','Mes','Valor']]

    datos_w=pd.pivot_table(datos, index='Mes', columns='Propiedad', values='Valor', aggfunc=np.sum)

    fig_industria = px.bar(
      datos_w,
      x=datos_w.index,
      y=['MPB Propia','MPB Terceros'],
      title="Indicador Materia Prima Bruta por origen",
      labels={'Mes':'Mes','value':'Ton','variable':'Origen'},
      color_discrete_map={"MPB Propia": "#00609C", "MPB Terceros": "#F4D19F"},
      template="simple_white",
      width=700, 
      height=500,
      )
                
    fig_industria.update_layout(
        font=dict(
            family="Arial",
            size=11
        )
    )
    
    import json
    fig_as_json = json.dumps(fig_industria, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json

def grafico_MMPP_origen_emp(empresa = 'Manuelita',ano = 2021):
    import pandas as pd
    import numpy as np

    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv")
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)
    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    idx = pd.IndexSlice
    
    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
        aux=0
        for i in range(len(df.index)):
            print(i)
            if df.iloc[i][('N Empresas','')] < thresh:
                df = df.drop(df.index[i-aux], axis=0)
                aux +=1
        return df
    
    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])

    dict_propiedad = {
    'Ingreso Materia Bruta Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta o Pagada Propia':'Materia Prima Propia',
    'Unidades por kilo mmpp propia*':'Materia Prima Propia',
    'Ingreso Materia Prima Bruta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Materia Prima Terceros',
    'Unidades por kilo mmpp terceros*':'Materia Prima Terceros',
    'Ingreso Materia Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Bruta Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Terceros':'Materia Prima Terceros',
    '% Rechazo Materia Prima Total':'Total Materia Prima',
    'rechazo propio': 'Materia Prima Propia',
    'rechazo terceros': 'Materia Prima Terceros'
    }

    dict_indicadores_2 = {
    'Ingreso Materia Bruta Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Propia':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp propia*':'Unidades por kilo',
    'Ingreso Materia Prima Bruta Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp terceros*':'Unidades por kilo',
    'Ingreso Materia Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Propia':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Terceros':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Bruta Propia':'Ingreso Materia Prima Bruta',
    '% Rechazo Materia Prima Propia':'% Rechazo MMPP',
    '% Rechazo Materia Prima Terceros':'% Rechazo MMPP',
    '% Rechazo Materia Prima Total':'% Rechazo MMPP',
    'rechazo propio': "% Rechazo MMPP",
    'rechazo terceros': "% Rechazo MMPP"
    }


    datos = ingresos_mmpp[(ingresos_mmpp.Tipo== 'MP Bruta')].copy()[['Empresa','Propiedad','Mes','Valor']]

    import plotly.express as px
    from plotly.subplots import make_subplots
    import plotly.graph_objects as go

    datos_w=pd.pivot_table(datos[datos.Empresa == empresa], index='Mes', columns='Propiedad', values='Valor', aggfunc=np.sum)

    fig_industria2 = px.bar(datos_w, x=datos_w.index,
                y=['MPB Propia','MPB Terceros'], title="Indicador Materia Prima Bruta por origen "+empresa,
                labels={'Mes':'Mes','value':'Ton','variable':'Origen'},
                color_discrete_map={"MPB Propia": "#C45130", "MPB Terceros": "#FFAA61"},
                template="simple_white",
                width=700, 
                height=500)
                
    fig_industria2.update_layout(
        font=dict(
            family="Arial",
            size=11
        )
    )
    
    import json
    import plotly
    
    fig_as_json = json.dumps(fig_industria2, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json
  
def grafico_MMPP_Bruta_emp(empresa = 'Manuelita',ano = 2021):
    import pandas as pd
    import numpy as np

    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv")
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    idx = pd.IndexSlice
    
    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
        aux=0
        for i in range(len(df.index)):
            print(i)
            if df.iloc[i][('N Empresas','')] < thresh:
                df = df.drop(df.index[i-aux], axis=0)
                aux +=1
        return df
    
    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])
    
    dict_propiedad = {
    'Ingreso Materia Bruta Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta o Pagada Propia':'Materia Prima Propia',
    'Unidades por kilo mmpp propia*':'Materia Prima Propia',
    'Ingreso Materia Prima Bruta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Materia Prima Terceros',
    'Unidades por kilo mmpp terceros*':'Materia Prima Terceros',
    'Ingreso Materia Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Bruta Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Terceros':'Materia Prima Terceros',
    '% Rechazo Materia Prima Total':'Total Materia Prima',
    'rechazo propio': 'Materia Prima Propia',
    'rechazo terceros': 'Materia Prima Terceros'
    }
    
    dict_indicadores_2 = {
    'Ingreso Materia Bruta Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Propia':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp propia*':'Unidades por kilo',
    'Ingreso Materia Prima Bruta Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp terceros*':'Unidades por kilo',
    'Ingreso Materia Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Propia':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Terceros':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Bruta Propia':'Ingreso Materia Prima Bruta',
    '% Rechazo Materia Prima Propia':'% Rechazo MMPP',
    '% Rechazo Materia Prima Terceros':'% Rechazo MMPP',
    '% Rechazo Materia Prima Total':'% Rechazo MMPP',
    'rechazo propio': "% Rechazo MMPP",
    'rechazo terceros': "% Rechazo MMPP"
    }


    datos = ingresos_mmpp.copy()
    datos['Indicadores'] = [dict_indicadores_2[datos.Indicador.iloc[i]] for i in range(len(datos.Indicador))]
    datos['Propietario'] = [dict_propiedad[datos.Indicador.iloc[i]] for i in range(len(datos.Indicador))]

    datos = datos.drop_duplicates(subset=['Indicador','Valor','Mes','Ano','Empresa'])


    import plotly.express as px
    from plotly.subplots import make_subplots
    import plotly.graph_objects as go

    datos_w = datos[(datos.Empresa == empresa) & (datos.Indicadores == 'Unidades por kilo')].groupby(['Mes','Propietario']).Valor.sum().unstack()
    datos_w = datos_w.drop_duplicates()

    fig_industria = px.bar(datos_w, x=datos_w.index,
                y=['Materia Prima Propia','Materia Prima Terceros'], title="Indicador Materia Prima Bruta por origen "+empresa,
                labels={'Mes':'Mes','value':'Unidades por kilo','variable':'Origen'},
                color_discrete_map={"Materia Prima Propia": "#C45130", "Materia Prima Terceros": "#FFAA61"},
                template="simple_white",
                width=700, 
                height=500)
                
    fig_industria.update_layout(
        font=dict(
            family="Arial",
            size=11
        )
    )

    import json
    import plotly
    
    fig_as_json = json.dumps(fig_industria, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json

def grafico_unidades_por_kilo(empresa = 'Manuelita',ano = 2021):
    import pandas as pd
    import numpy as np

    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv")
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    idx = pd.IndexSlice
    
    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
        aux=0
        for i in range(len(df.index)):
            print(i)
            if df.iloc[i][('N Empresas','')] < thresh:
                df = df.drop(df.index[i-aux], axis=0)
                aux +=1
        return df
    
    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])
    
    dict_propiedad = {
    'Ingreso Materia Bruta Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta o Pagada Propia':'Materia Prima Propia',
    'Unidades por kilo mmpp propia*':'Materia Prima Propia',
    'Ingreso Materia Prima Bruta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Materia Prima Terceros',
    'Unidades por kilo mmpp terceros*':'Materia Prima Terceros',
    'Ingreso Materia Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Bruta Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Terceros':'Materia Prima Terceros',
    '% Rechazo Materia Prima Total':'Total Materia Prima',
    'rechazo propio': 'Materia Prima Propia',
    'rechazo terceros': 'Materia Prima Terceros'
    }
    
    dict_indicadores_2 = {
    'Ingreso Materia Bruta Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Propia':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp propia*':'Unidades por kilo',
    'Ingreso Materia Prima Bruta Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp terceros*':'Unidades por kilo',
    'Ingreso Materia Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Propia':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Terceros':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Bruta Propia':'Ingreso Materia Prima Bruta',
    '% Rechazo Materia Prima Propia':'% Rechazo MMPP',
    '% Rechazo Materia Prima Terceros':'% Rechazo MMPP',
    '% Rechazo Materia Prima Total':'% Rechazo MMPP',
    'rechazo propio': "% Rechazo MMPP",
    'rechazo terceros': "% Rechazo MMPP"
    }


    datos = ingresos_mmpp.copy()
    datos['Indicadores'] = [dict_indicadores_2[datos.Indicador.iloc[i]] for i in range(len(datos.Indicador))]
    datos['Propietario'] = [dict_propiedad[datos.Indicador.iloc[i]] for i in range(len(datos.Indicador))]

    datos = datos.drop_duplicates(subset=['Indicador','Valor','Mes','Ano','Empresa'])

    import plotly.express as px
    from plotly.subplots import make_subplots
    import plotly.graph_objects as go

    #Filtrar data
    datos2 = datos[datos.Indicadores != 'Ingreso Materia Prima Bruta']
    datos2 = pd.pivot_table(datos2, index=['Mes','Empresa'], columns=['Propietario','Indicadores'], values='Valor', aggfunc=np.sum).fillna(0)

    df_unidades = pd.DataFrame(columns = ['Mes','Unidades por kilo Propia', 'Unidades por kilo Terceros'])

    for i,j in datos2.index:
        df_aux = datos2.loc[idx[i,:],:]['Materia Prima Propia']
        df_auxa = datos2.loc[idx[i,:],:]['Materia Prima Terceros']
        new_row = {
            'Mes':i,
            'Unidades por kilo Propia': (df_aux['Ingreso Materia Prima Neta']*df_aux['Unidades por kilo']).sum()/df_aux['Ingreso Materia Prima Neta'].sum(),
            'Unidades por kilo Terceros': (df_auxa['Ingreso Materia Prima Neta']*df_auxa['Unidades por kilo']).sum()/df_auxa['Ingreso Materia Prima Neta'].sum()
        }
        df_unidades = df_unidades.append(new_row, ignore_index = True)
        
    df_unidades = df_unidades.drop_duplicates()
    fig_industria = px.bar(df_unidades, x='Mes',
                y=['Unidades por kilo Propia','Unidades por kilo Terceros'], title="Unidades por kilo ",
                labels={'Mes':'Mes','value':'Unidades por kilo','variable':'Origen'},
                color_discrete_map={"Unidades por kilo Propia": "#00609C", "Unidades por kilo Terceros": "#F4D19F"},
                template="simple_white",
                width=700, 
                height=500)
                
                
    fig_industria.update_layout(
        font=dict(
            family="Arial",
            size=11
        )       
    )    

    import json
    import plotly
    
    fig_as_json = json.dumps(fig_industria, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json
  
def grafico_rechazo(ano = 2021):
    
    import pandas as pd
    import numpy as np

    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv") 
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    idx = pd.IndexSlice
    
    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
        aux=0
        for i in range(len(df.index)):
            print(i)
            if df.iloc[i][('N Empresas','')] < thresh:
                df = df.drop(df.index[i-aux], axis=0)
                aux +=1
        return df
    
    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])
    
    dict_propiedad = {
    'Ingreso Materia Bruta Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta o Pagada Propia':'Materia Prima Propia',
    'Unidades por kilo mmpp propia*':'Materia Prima Propia',
    'Ingreso Materia Prima Bruta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Materia Prima Terceros',
    'Unidades por kilo mmpp terceros*':'Materia Prima Terceros',
    'Ingreso Materia Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Bruta Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Terceros':'Materia Prima Terceros',
    '% Rechazo Materia Prima Total':'Total Materia Prima',
    'rechazo propio': 'Materia Prima Propia',
    'rechazo terceros': 'Materia Prima Terceros'
    }
    
    dict_indicadores_2 = {
    'Ingreso Materia Bruta Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Propia':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp propia*':'Unidades por kilo',
    'Ingreso Materia Prima Bruta Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp terceros*':'Unidades por kilo',
    'Ingreso Materia Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Propia':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Terceros':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Bruta Propia':'Ingreso Materia Prima Bruta',
    '% Rechazo Materia Prima Propia':'% Rechazo MMPP',
    '% Rechazo Materia Prima Terceros':'% Rechazo MMPP',
    '% Rechazo Materia Prima Total':'% Rechazo MMPP',
    'rechazo propio': "% Rechazo MMPP",
    'rechazo terceros': "% Rechazo MMPP"
    }

    # rechazo por origen empresa 

    #Rechazo por origen
    datos = ingresos_mmpp.copy()
    datos['Indicadores'] = [dict_indicadores_2[datos.Indicador.iloc[i]] for i in range(len(datos.Indicador))]
    datos['Propietario'] = [dict_propiedad[datos.Indicador.iloc[i]] for i in range(len(datos.Indicador))]
    datos = datos.drop_duplicates(subset=['Indicador','Valor','Mes','Ano','Empresa'])

    datos = pd.pivot_table(datos[datos.Indicadores != 'Unidades por kilo'], index=['Mes'], columns=['Propietario','Indicadores'], values='Valor', aggfunc=np.sum)
    for i in datos.index:
        datos.loc[i,('Materia Prima Propia','% Rechazo mmpp')] = (datos.loc[i,('Materia Prima Propia','Ingreso Materia Prima Bruta')] - datos.loc[i,('Materia Prima Propia','Ingreso Materia Prima Neta')]) /datos.loc[i,('Materia Prima Propia','Ingreso Materia Prima Bruta')]*100
        datos.loc[i,('Materia Prima Terceros','% Rechazo mmpp')] = (datos.loc[i,('Materia Prima Terceros','Ingreso Materia Prima Bruta')] - datos.loc[i,('Materia Prima Terceros','Ingreso Materia Prima Neta')]) /datos.loc[i,('Materia Prima Propia','Ingreso Materia Prima Bruta')]*100

    datos = datos.loc[idx[:],idx[:,'% Rechazo mmpp']]
    datos.columns = ['% Rechazo mmpp Propia','% Rechazo mmpp Terceros']

    import plotly.express as px
    fig = px.line(datos, x=datos.index, y=['% Rechazo mmpp Propia','% Rechazo mmpp Terceros'], markers=True,
                    title="Porcentaje de rechazo por origen",
                    labels={'Mes':'Mes','value':'% Rechazo MMPP','variable':'Origen'},
                    color_discrete_map={'% Rechazo mmpp Propia': "#00609C", '% Rechazo mmpp Terceros': "#F4D19F"},
                    template="simple_white",
                    width=700, 
                    height=500,)
                    
                
    fig.update_layout(
        font=dict(
            family="Arial",
            size=11
        )
    )

    import json
    import plotly
    
    fig_as_json = json.dumps(fig, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json
  
def grafico_rechazo_origen(empresa = 'Manuelita' ,ano = 2021):
    import pandas as pd
    import numpy as np

    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv")
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    idx = pd.IndexSlice
    
    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
        aux=0
        for i in range(len(df.index)):
            print(i)
            if df.iloc[i][('N Empresas','')] < thresh:
                df = df.drop(df.index[i-aux], axis=0)
                aux +=1
        return df
    
    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])
    
    dict_propiedad = {
    'Ingreso Materia Bruta Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta o Pagada Propia':'Materia Prima Propia',
    'Unidades por kilo mmpp propia*':'Materia Prima Propia',
    'Ingreso Materia Prima Bruta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Materia Prima Terceros',
    'Unidades por kilo mmpp terceros*':'Materia Prima Terceros',
    'Ingreso Materia Prima Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Neta Propia':'Materia Prima Propia',
    'Ingreso Materia Prima Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Neta Terceros':'Materia Prima Terceros',
    'Ingreso Materia Prima Bruta Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Propia':'Materia Prima Propia',
    '% Rechazo Materia Prima Terceros':'Materia Prima Terceros',
    '% Rechazo Materia Prima Total':'Total Materia Prima',
    'rechazo propio': 'Materia Prima Propia',
    'rechazo terceros': 'Materia Prima Terceros'
    }
    
    dict_indicadores_2 = {
    'Ingreso Materia Bruta Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Propia':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp propia*':'Unidades por kilo',
    'Ingreso Materia Prima Bruta Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta o Pagada Terceros':'Ingreso Materia Prima Neta',
    'Unidades por kilo mmpp terceros*':'Unidades por kilo',
    'Ingreso Materia Prima Propia':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Propia':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Terceros':'Ingreso Materia Prima Bruta',
    'Ingreso Materia Prima Neta Terceros':'Ingreso Materia Prima Neta',
    'Ingreso Materia Prima Bruta Propia':'Ingreso Materia Prima Bruta',
    '% Rechazo Materia Prima Propia':'% Rechazo MMPP',
    '% Rechazo Materia Prima Terceros':'% Rechazo MMPP',
    '% Rechazo Materia Prima Total':'% Rechazo MMPP',
    'rechazo propio': "% Rechazo MMPP",
    'rechazo terceros': "% Rechazo MMPP"
    }


    # rechazo por origen empresa 

    datos = ingresos_mmpp[ingresos_mmpp.Empresa == empresa].copy()
    datos['Indicadores'] = [dict_indicadores_2[datos.Indicador.iloc[i]] for i in range(len(datos.Indicador))]
    datos['Propietario'] = [dict_propiedad[datos.Indicador.iloc[i]] for i in range(len(datos.Indicador))]
    datos = datos.drop_duplicates(subset=['Indicador','Valor','Mes','Ano','Empresa'])

    datos = pd.pivot_table(datos[datos.Indicadores != 'Unidades por kilo'], index=['Mes'], columns=['Propietario','Indicadores'], values='Valor', aggfunc=np.sum)
    for i in datos.index:
        datos.loc[i,('Materia Prima Propia','% Rechazo mmpp')] = (datos.loc[i,('Materia Prima Propia','Ingreso Materia Prima Bruta')] - datos.loc[i,('Materia Prima Propia','Ingreso Materia Prima Neta')]) /datos.loc[i,('Materia Prima Propia','Ingreso Materia Prima Bruta')]*100
        datos.loc[i,('Materia Prima Terceros','% Rechazo mmpp')] = (datos.loc[i,('Materia Prima Terceros','Ingreso Materia Prima Bruta')] - datos.loc[i,('Materia Prima Terceros','Ingreso Materia Prima Neta')]) /datos.loc[i,('Materia Prima Propia','Ingreso Materia Prima Bruta')]*100

    datos = datos.loc[idx[:],idx[:,'% Rechazo mmpp']]
    datos.columns = ['% Rechazo mmpp Propia','% Rechazo mmpp Terceros']

    import plotly.express as px
    fig = px.line(datos, x=datos.index, y=['% Rechazo mmpp Propia','% Rechazo mmpp Terceros'], markers=True,
                    title="Porcentaje de rechazo por origen "+empresa,
                    labels={'Mes':'Mes','value':'% Rechazo MMPP','variable':'Origen'},
                    color_discrete_map={'% Rechazo mmpp Propia': "#00609C", '% Rechazo mmpp Terceros': "#F4D19F"},
                    template="simple_white",
                    width=700, 
                    height=500)
                    
                    
                                
    fig.update_layout(
        font=dict(
            family="Arial",
            size=11
        )
    )
    
    import json
    import plotly
    
    fig_as_json = json.dumps(fig, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json

def resumen_productos(ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice
    
    dict_meses = {
        1:'Ene',
        2:'Feb',
        3:'Mar',
        4:'Abr',
        5:'May',
        6:'Jun',
        7:'Jul',
        8:'Ago',
        9:'Sep',
        10:'Oct',
        11:'Nov',
        12:'Dic',
        2021: '2021'
    }
    
     # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
        aux=0
        for i in range(len(df.index)):
            print(i)
            if df.iloc[i][('N Empresas','')] < thresh:
                df = df.drop(df.index[i-aux], axis=0)
                aux +=1
        return df
    
    producto = pd.read_csv("Procesos_Producto_Consolidado.csv")
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)

    producto.head()

    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    filtro = (producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha')
    datos = producto.loc[filtro].pivot_table(index='Producto',columns='Mes', values='Valor', aggfunc=np.sum)
    total = datos.sum(axis=1).round(2)
    average = datos.mean(axis=1).round(2)
    datos[13] = total
    datos[14] = average
    datos

    filtro = (producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha')
    datos2 = producto.loc[filtro].pivot_table(index='Producto',columns='Mes', values='Valor', aggfunc=np.sum)
    total2 = datos2.sum(axis=1).round(2)
    average2 = datos2.mean(axis=1).round(2)
    datos2[13] = total2
    datos2[14] = average2
    datos2 = datos2.transform(lambda x: (x / x.sum()).round(2))*100
    datos2

    largo = len(datos.columns)
    new_index = []
    for i in range(1, largo+1):
        if i == largo - 1:
            new_index.append(('Total','Ton'))
            new_index.append(('Total','%'))
        elif i == largo:
            new_index.append(('Promedio','Ton'))
            new_index.append(('Promedio','%'))
        else: 
            new_index.append((dict_meses[i],'Ton'))
            new_index.append((dict_meses[i],'%'))

    pv3 = pd.concat([datos, datos2], axis=1).sort_index(level=0,axis=1)
    pv3.columns = pd.MultiIndex.from_tuples(new_index)

    largo = len(pv3.columns)
    for i in range(1,largo+1,2):
        if pv3.iloc[0,i]+pv3.iloc[1,i] + pv3.iloc[2,i]  != 100.0: 
            aux_1 = pv3.iloc[:,[i]].copy()
            aux_2 = pv3.iloc[:,[i-1]].copy()
            pv3.iloc[:,[i-1]] = aux_1.values
            pv3.iloc[:,[i]] = aux_2.values
            

    # Corregir orden de columnas
    largo = len(pv3.columns)-1
    for i in range(1,largo+1,2):
        if pv3.iloc[0,i]+pv3.iloc[1,i] != 100.0: 
            aux_1 = pv3.iloc[:,[i]].copy()
            aux_2 = pv3.iloc[:,[i-1]].copy()
            pv3.iloc[:,[i-1]] = aux_1.values
            pv3.iloc[:,[i]] = aux_2.values

    #Agregar el contador

    contador = producto.loc[filtro].pivot_table(index='Producto', values='Empresa', aggfunc=lambda x: len(x.unique()))
    columnas = [('N Empresas','')]
    contador.columns = pd.MultiIndex.from_tuples(columnas)
    df_filtro = pv3.merge(contador, how='left',left_index=True, right_index=True)

    # Funcion para remover filas con indicadores para menos de 3 empresas
    pv3 = remover_filas(df_filtro)
            
    pv3 = pv3.reset_index(level=None, drop=False)
    pv3.fillna("", inplace=True)
    
    return pv3

def resumen_mensual_producto(empresa = 'Manuelita',ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice
    empresa = 'Data Mussel'
    dict_meses = {
        1:'Ene',
        2:'Feb',
        3:'Mar',
        4:'Abr',
        5:'May',
        6:'Jun',
        7:'Jul',
        8:'Ago',
        9:'Sep',
        10:'Oct',
        11:'Nov',
        12:'Dic',
        2021: '2021'
    }
    
    def remover_filas(df, thresh=3):
      aux=0
      for i in range(len(df.index)):
        if df.iloc[i][('N Empresas','')] < thresh:
          df = df.drop(df.index[i-aux], axis=0)
          aux +=1
      return df
    
    producto = pd.read_csv("Procesos_Producto_Consolidado.csv") 
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)
    producto.head()

    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    filtro = (producto.Empresa == empresa) & ((producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha'))
    datos = producto.loc[filtro].pivot_table(index='Producto',columns='Mes', values='Valor', aggfunc=np.sum)
    total = datos.sum(axis=1).round(2)
    average = datos.mean(axis=1).round(2)
    datos[13] = total
    datos[14] = average
    len(datos.columns)

    filtro = (producto.Empresa == empresa) & ((producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha'))
    datos2 = producto.loc[filtro].pivot_table(index='Producto',columns='Mes', values='Valor', aggfunc=np.sum)
    total2 = datos2.sum(axis=1).round(2)
    average2 = datos2.mean(axis=1).round(2)
    datos2[13] = total2
    datos2[14] = average2
    datos2 = datos2.transform(lambda x: (x / x.sum()).round(2))*100

    largo = len(datos.columns)
    new_index = []
    for i in range(1, largo+1):
      if i == largo - 1:
        new_index.append(('Total','Ton'))
        new_index.append(('Total','%'))
      elif i == largo:
        new_index.append(('Promedio','Ton'))
        new_index.append(('Promedio','%'))
      else: 
        new_index.append((dict_meses[i],'Ton'))
        new_index.append((dict_meses[i],'%'))
    
    pv3 = pd.concat([datos, datos2], axis=1).sort_index(level=0,axis=1)
    pv3.columns = pd.MultiIndex.from_tuples(new_index)

    largo = len(pv3.columns)
    for i in range(1,largo+1,2):
      if pv3.iloc[0,i]+pv3.iloc[1,i] + pv3.iloc[2,i]  != 100.0: 
        aux_1 = pv3.iloc[:,[i]].copy()
        aux_2 = pv3.iloc[:,[i-1]].copy()
        pv3.iloc[:,[i-1]] = aux_1.values
        pv3.iloc[:,[i]] = aux_2.values

    pv3 = pv3.reset_index(level=None, drop=False)
    pv3.fillna("", inplace=True)

    return pv3
  
def resumen_mensual_otras(empresa = 'Manuelita',ano = 2021):

    import pandas as pd
    import numpy as np
    ano = 2021
    idx = pd.IndexSlice
    
    dict_meses = {
        1:'Ene',
        2:'Feb',
        3:'Mar',
        4:'Abr',
        5:'May',
        6:'Jun',
        7:'Jul',
        8:'Ago',
        9:'Sep',
        10:'Oct',
        11:'Nov',
        12:'Dic',
        2021: '2021'
    }

    producto = pd.read_csv("Procesos_Producto_Consolidado.csv")
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    filtro = (producto.Empresa != empresa) & ((producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha'))
    datos = producto.loc[filtro].pivot_table(index='Producto',columns='Mes', values='Valor', aggfunc=np.sum)
    total = datos.sum(axis=1).round(2)
    average = datos.mean(axis=1).round(2)
    datos[13] = total
    datos[14] = average
    datos

    filtro = (producto.Empresa != empresa) & ((producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha'))
    datos2 = producto.loc[filtro].pivot_table(index='Producto',columns='Mes', values='Valor', aggfunc=np.sum)
    total2 = datos2.sum(axis=1).round(2)
    average2 = datos2.mean(axis=1).round(2)
    datos2[13] = total2
    datos2[14] = average2
    datos2 = datos2.transform(lambda x: (x / x.sum()).round(2))*100
    datos2

    largo = len(datos.columns)
    new_index = []
    for i in range(1, largo+1):
        if i == largo - 1:
            new_index.append(('Total','Ton'))
            new_index.append(('Total','%'))
        elif i == largo:
            new_index.append(('Promedio','Ton'))
            new_index.append(('Promedio','%'))
        else: 
            new_index.append((dict_meses[i],'Ton'))
            new_index.append((dict_meses[i],'%'))

    pv3 = pd.concat([datos, datos2], axis=1).sort_index(level=0,axis=1)
    pv3.columns = pd.MultiIndex.from_tuples(new_index)

    largo = len(pv3.columns)
    for i in range(1,largo+1,2):
        if pv3.iloc[0,i]+pv3.iloc[1,i] + pv3.iloc[2,i]  != 100.0: 
            aux_1 = pv3.iloc[:,[i]].copy()
            aux_2 = pv3.iloc[:,[i-1]].copy()
            pv3.iloc[:,[i-1]] = aux_1.values
            pv3.iloc[:,[i]] = aux_2.values
            
    largo = len(pv3.columns)-1
    for i in range(1,largo+1,2):
        if pv3.iloc[0,i]+pv3.iloc[1,i] != 100.0: 
            aux_1 = pv3.iloc[:,[i]].copy()
            aux_2 = pv3.iloc[:,[i-1]].copy()
            pv3.iloc[:,[i-1]] = aux_1.values
            pv3.iloc[:,[i]] = aux_2.values


    contador = producto.loc[filtro].pivot_table(index='Producto', values='Empresa', aggfunc=lambda x: len(x.unique()))
    columnas = [('N Empresas','')]
    contador.columns = pd.MultiIndex.from_tuples(columnas)
    df_filtro = pv3.merge(contador, how='left',left_index=True, right_index=True)

    #Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas(df, thresh=3):
        aux=0
        for i in range(len(df.index)):
            if df.iloc[i][('N Empresas','')] < thresh:
                df = df.drop(df.index[i-aux], axis=0)
                aux +=1
            return df
    pv3 = remover_filas(df_filtro)
        
    pv3 = pv3.reset_index(level=None, drop=False)
    pv3.fillna("", inplace=True)

    return pv3

def detalle_productos(empresa = 'Manuelita',ano = 2021):

    import pandas as pd
    import numpy as np

    idx = pd.IndexSlice
    
    dict_meses = {
        1:'Ene',
        2:'Feb',
        3:'Mar',
        4:'Abr',
        5:'May',
        6:'Jun',
        7:'Jul',
        8:'Ago',
        9:'Sep',
        10:'Oct',
        11:'Nov',
        12:'Dic',
        2021: '2021'
    }
    
    
    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas_single(df, thresh=3):
      aux = 0
      for i in range(len(df)):
        if (df.iloc[i-aux]['N Empresas'] < thresh) & (df.iloc[i-aux]['N Empresas'] != np.nan) :
          df = df.drop(df.index[i-aux])
          aux +=1
      return df

    producto = pd.read_csv("Procesos_Producto_Consolidado.csv")
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)

    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    producto.drop_duplicates(inplace=True)

    filtro = (producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha')
    producto.drop_duplicates(inplace=True)
    datos = producto.loc[filtro].pivot_table(index=['Producto','Calibre'], values='Valor', aggfunc=np.sum)
    idx = pd.IndexSlice

    #Agregar el contador

    contador = producto.loc[filtro].pivot_table(index=['Producto','Calibre'], values='Empresa', aggfunc=lambda x: len(x.unique()))
    columnas = ['N Empresas']
    contador.columns = columnas
    df_filtro = datos.merge(contador, how='left',left_index=True, right_index=True)

    # Funcion para remover filas con indicadores para menos de 3 empresas
    datos = remover_filas_single(df_filtro)

    for p, c in datos.index:
        datos.loc[(p, c),'%'] = datos.loc[(p, c),'Valor']*100/datos.loc[idx[p,:],'Valor'].sum()
        
    #Reindex
    try: 
        new_index = [(       'Carne',   '50-100 UNI/KG'),
                (       'Carne',  '100-200 UNI/KG'),
                (       'Carne',  '200-300 UNI/KG'),
                (       'Carne',  '300-500 UNI/KG'),
                (       'Carne', '500-1000 UNI/KG'),
                (       'Carne',      'INDUSTRIAL'),
                (      'Entero',       'CON SALSA'),
                (      'Entero',         'NATURAL'),
                ('Media Concha',    '40-60 UNI/KG'),
                ('Media Concha',    '60-80 UNI/KG'),
                ('Media Concha',   '80-100 UNI/KG'),
                ('Media Concha',   '100 UP UNI/KG')]

        datos = datos.loc[new_index]

        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
    except:
        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
        
    # Tabla plana para lectura en R    
    datos_ind = datos.reset_index(level=None, drop=False).fillna(0)

    # Detalle productos EMPRESA

    producto = pd.read_csv("Procesos_Producto_Consolidado.csv") 
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)

    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    filtro = (producto.Empresa == empresa) & ((producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha'))
    producto.drop_duplicates(inplace=True)
    datos = producto.loc[filtro].pivot_table(index=['Producto','Calibre'], values='Valor', aggfunc=np.sum)

    for p, c in datos.index:
        datos.loc[(p, c),'%'] = datos.loc[(p, c),'Valor']*100/datos.loc[idx[p,:],'Valor'].sum()
        
    #Reindex
    try: 
        new_index = [(       'Carne',   '50-100 UNI/KG'),
                (       'Carne',  '100-200 UNI/KG'),
                (       'Carne',  '200-300 UNI/KG'),
                (       'Carne',  '300-500 UNI/KG'),
                (       'Carne', '500-1000 UNI/KG'),
                (       'Carne',      'INDUSTRIAL'),
                (      'Entero',       'CON SALSA'),
                (      'Entero',         'NATURAL'),
                ('Media Concha',    '40-60 UNI/KG'),
                ('Media Concha',    '60-80 UNI/KG'),
                ('Media Concha',   '80-100 UNI/KG'),
                ('Media Concha',   '100 UP UNI/KG')]

        datos = datos.loc[new_index]

        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
    except:
        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
        
    datos_emp = datos.reset_index(level=None, drop=False).fillna(0)

    ######## Detalle productos Otras empresas

    producto = pd.read_csv("Procesos_Producto_Consolidado.csv")
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)


    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    filtro = (producto.Empresa != empresa) & ((producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha'))
    producto.drop_duplicates(inplace=True)
    datos = producto.loc[filtro].pivot_table(index=['Producto','Calibre'], values='Valor', aggfunc=np.sum)

    #Agregar el contador

    contador = producto.loc[filtro].pivot_table(index=['Producto','Calibre'], values='Empresa', aggfunc=lambda x: len(x.unique()))
    columnas = ['N Empresas']
    contador.columns = columnas
    df_filtro = datos.merge(contador, how='left',left_index=True, right_index=True)

    # Funcion para remover filas con indicadores para menos de 3 empresas
    datos = remover_filas_single(df_filtro)

    for p, c in datos.index:
        datos.loc[(p, c),'%'] = datos.loc[(p, c),'Valor']*100/datos.loc[idx[p,:],'Valor'].sum()
        
    #Reindex
    try: 
        new_index = [(       'Carne',   '50-100 UNI/KG'),
                (       'Carne',  '100-200 UNI/KG'),
                (       'Carne',  '200-300 UNI/KG'),
                (       'Carne',  '300-500 UNI/KG'),
                (       'Carne', '500-1000 UNI/KG'),
                (       'Carne',      'INDUSTRIAL'),
                (      'Entero',       'CON SALSA'),
                (      'Entero',         'NATURAL'),
                ('Media Concha',    '40-60 UNI/KG'),
                ('Media Concha',    '60-80 UNI/KG'),
                ('Media Concha',   '80-100 UNI/KG'),
                ('Media Concha',   '100 UP UNI/KG')]

        datos = datos.loc[new_index]

        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
    except:
        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
        
    datos_otras = datos.reset_index(level=None, drop=False).fillna(0)

    ######## Juntar los dataframes  
    # Unir las ultimas 3 tablas
    datos_all = datos_ind.merge(datos_emp, how='outer', on=['Producto','Calibre'])
    datos_all = datos_all.merge(datos_otras, how='outer',on=['Producto','Calibre'])

    try:
      datos_all = datos_all.loc[[('Materia Prima Propia', 'Ingreso Materia Prima Bruta'),
      ('Materia Prima Propia', 'Ingreso Materia Prima Neta'),
      ('Materia Prima Propia', 'Unidades por kilo'),
      ('Materia Prima Propia', '% Rechazo mmpp'),
      ('Materia Prima Propia', '% MP Bruta Propia sobre Total MP Bruta'),
      ('Materia Prima Terceros', 'Ingreso Materia Prima Bruta'),
      ('Materia Prima Terceros', 'Ingreso Materia Prima Neta'),
      ('Materia Prima Terceros', 'Unidades por kilo'),
      ('Materia Prima Terceros', '% Rechazo mmpp'),
      ('Materia Prima Terceros', '% MP Bruta Propia sobre Total MP Bruta'),
      ('Total Materia Prima', 'Ingreso Materia Prima Bruta'),
      ('Total Materia Prima', 'Ingreso Materia Prima Neta'),
      ('Total Materia Prima', '% Rechazo mmpp'),
      ],:].fillna("")
    except:
      datos_all.fillna(0)
    
    datos_all = datos_all.reset_index(level=None, drop=True).fillna(0)
    datos_all.Producto = ["" if datos_all.Producto.iloc[i] == datos_all.Producto.iloc[i-1] else datos_all.Producto.iloc[i] for i in range(len(datos_all.Producto))]
    datos_all.drop(['N Empresas_x'], axis=1, inplace= True)
    
    datos_all = datos_all.iloc[:, [0,1,2,3,4,5,6,8,7]]

    return datos_all

def detalle_productos_mes(empresa = 'Manuelita', mes = 'Ene',ano = 2021):

    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice
    
    #Cambiar a numero
    dict_mes_num = {
        'Ene':1,
        'Feb':2,
        'Mar':3,
        'Abr':4,
        'May':5,
        'Jun':6,
        'Jul':7,
        'Ago':8,
        'Sep':9,
        'Oct':10,
        'Nov':11,
        'Dic':12
    }

    mes = dict_mes_num[mes]
    producto = pd.read_csv("Procesos_Producto_Consolidado.csv") 
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)

    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    producto.drop_duplicates(inplace=True)
    filtro = (producto.Mes == mes) & ((producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha'))
    producto.drop_duplicates(inplace=True)
    datos = producto.loc[filtro].pivot_table(index=['Producto','Calibre'], values='Valor', aggfunc=np.sum)
    idx = pd.IndexSlice

    #Agregar el contador

    contador = producto.loc[filtro].pivot_table(index=['Producto','Calibre'], values='Empresa', aggfunc=lambda x: len(x.unique()))
    columnas = ['N Empresas']
    contador.columns = columnas
    df_filtro = datos.merge(contador, how='left',left_index=True, right_index=True)

    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas_single(df, thresh=3):
      aux = 0
      for i in range(len(df)):
        if (df.iloc[i-aux]['N Empresas'] < thresh) & (df.iloc[i-aux]['N Empresas'] != np.nan) :
          df = df.drop(df.index[i-aux])
          aux +=1
      return df
    
    datos = remover_filas_single(df_filtro)

    for p, c in datos.index:
        datos.loc[(p, c),'%'] = datos.loc[(p, c),'Valor']*100/datos.loc[idx[p,:],'Valor'].sum()
        
    #Reindex
    try: 
        new_index = [(       'Carne',   '50-100 UNI/KG'),
                (       'Carne',  '100-200 UNI/KG'),
                (       'Carne',  '200-300 UNI/KG'),
                (       'Carne',  '300-500 UNI/KG'),
                (       'Carne', '500-1000 UNI/KG'),
                (       'Carne',      'INDUSTRIAL'),
                (      'Entero',       'CON SALSA'),
                (      'Entero',         'NATURAL'),
                ('Media Concha',    '40-60 UNI/KG'),
                ('Media Concha',    '60-80 UNI/KG'),
                ('Media Concha',   '80-100 UNI/KG'),
                ('Media Concha',   '100 UP UNI/KG')]

        datos = datos.loc[new_index]

        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
    except:
        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
        
    datos_ind = datos.copy()

    #### EMPRESA 
    producto = pd.read_csv("Procesos_Producto_Consolidado.csv")
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)


    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    filtro = (producto.Mes == mes) & (producto.Empresa == empresa) & ((producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha'))
    producto.drop_duplicates(inplace=True)
    datos = producto.loc[filtro].pivot_table(index=['Producto','Calibre'], values='Valor', aggfunc=np.sum)

    for p, c in datos.index:
        datos.loc[(p, c),'%'] = datos.loc[(p, c),'Valor']*100/datos.loc[idx[p,:],'Valor'].sum()
        
    #Reindex
    try: 
        new_index = [(       'Carne',   '50-100 UNI/KG'),
                (       'Carne',  '100-200 UNI/KG'),
                (       'Carne',  '200-300 UNI/KG'),
                (       'Carne',  '300-500 UNI/KG'),
                (       'Carne', '500-1000 UNI/KG'),
                (       'Carne',      'INDUSTRIAL'),
                (      'Entero',       'CON SALSA'),
                (      'Entero',         'NATURAL'),
                ('Media Concha',    '40-60 UNI/KG'),
                ('Media Concha',    '60-80 UNI/KG'),
                ('Media Concha',   '80-100 UNI/KG'),
                ('Media Concha',   '100 UP UNI/KG')]

        datos = datos.loc[new_index]

        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
    except:
        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
        
    datos_emp = datos
        
    ##### OTRAS EMPRESAS

    producto = pd.read_csv("Procesos_Producto_Consolidado.csv") 
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)


    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    filtro = (producto.Mes == mes) & (producto.Empresa != empresa) & ((producto.Producto == 'Carne') | (producto.Producto == 'Entero') | (producto.Producto == 'Media Concha'))
    producto.drop_duplicates(inplace=True)
    datos = producto.loc[filtro].pivot_table(index=['Producto','Calibre'], values='Valor', aggfunc=np.sum)

    #Agregar el contador

    contador = producto.loc[filtro].pivot_table(index=['Producto','Calibre'], values='Empresa', aggfunc=lambda x: len(x.unique()))
    columnas = ['N Empresas']
    contador.columns = columnas
    df_filtro = datos.merge(contador, how='left',left_index=True, right_index=True)

    # Funcion para remover filas con indicadores para menos de 3 empresas
    datos = remover_filas_single(df_filtro)

    for p, c in datos.index:
        datos.loc[(p, c),'%'] = datos.loc[(p, c),'Valor']*100/datos.loc[idx[p,:],'Valor'].sum()
        
    #Reindex
    try: 
        new_index = [(       'Carne',   '50-100 UNI/KG'),
                (       'Carne',  '100-200 UNI/KG'),
                (       'Carne',  '200-300 UNI/KG'),
                (       'Carne',  '300-500 UNI/KG'),
                (       'Carne', '500-1000 UNI/KG'),
                (       'Carne',      'INDUSTRIAL'),
                (      'Entero',       'CON SALSA'),
                (      'Entero',         'NATURAL'),
                ('Media Concha',    '40-60 UNI/KG'),
                ('Media Concha',    '60-80 UNI/KG'),
                ('Media Concha',   '80-100 UNI/KG'),
                ('Media Concha',   '100 UP UNI/KG')]

        datos = datos.loc[new_index]

        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
    except:
        datos = pd.concat([
            d.append(d.sum().rename((k, 'Total')))
            for k, d in datos.groupby(level=0)
        ]).append(datos.sum().rename(('General', 'Total'))).round()
        
    datos_otras = datos

    ###### UNIR TABLAS
    # Unir las ultimas 3 tablas
    datos_all = datos_ind.merge(datos_emp, how='outer', on=['Producto','Calibre'])
    datos_all = datos_all.merge(datos_otras, how='outer',on=['Producto','Calibre'])

    try:
        datos_all = datos_all.loc[[('Materia Prima Propia', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Propia', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Propia', 'Unidades por kilo'),
        ('Materia Prima Propia', '% Rechazo mmpp'),
        ('Materia Prima Propia', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Bruta'),
        ('Materia Prima Terceros', 'Ingreso Materia Prima Neta'),
        ('Materia Prima Terceros', 'Unidades por kilo'),
        ('Materia Prima Terceros', '% Rechazo mmpp'),
        ('Materia Prima Terceros', '% MP Bruta Propia sobre Total MP Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Bruta'),
        ('Total Materia Prima', 'Ingreso Materia Prima Neta'),
        ('Total Materia Prima', '% Rechazo mmpp'),
        ],:].fillna("")
    except:
        datos_all.fillna(0)
    
    
    datos_all_f = datos_all.reset_index(level=None, drop=False)
    datos_all_f.Producto = ["" if datos_all_f.Producto.iloc[i] == datos_all_f.Producto.iloc[i-1] else datos_all_f.Producto.iloc[i] for i in range(len(datos_all_f.Producto))]
    datos_all_f.drop(['N Empresas_x'], axis=1, inplace=True)
    datos_all_f = datos_all_f.iloc[:,[0,1,2,3,4,5,6,8,7]]

    return datos_all_f

def resumen_dotacion_industria(ano = 2020):
    import pandas as pd
    import numpy as np
    
    ### Carga Ingresos
    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv")
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)

    idx = pd.IndexSlice

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    # Agrupacion indicadores

    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])

    idx = pd.IndexSlice

    ##### Carga Producto Terminado
    
    producto = pd.read_csv("Procesos_Producto_Consolidado.csv")
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)

    producto.head()

    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]


    #### Carga dotacion

    dotacion = pd.read_csv("Procesos_Dotacion_Consolidado.csv") 
    dotacion = dotacion[dotacion['Ano'] == int(ano)]
    dotacion.Mes = dotacion.Mes.apply(lambda m: pd.to_numeric(m, errors='coerce'))
    dotacion.head()
    
    dict_meses = {
    1:'Ene',
    2:'Feb',
    3:'Mar',
    4:'Abr',
    5:'May',
    6:'Jun',
    7:'Jul',
    8:'Ago',
    9:'Sep',
    10:'Oct',
    11:'Nov',
    12:'Dic',
    }

    dict_cargos =  {
        'Operarios directos (produccion)': 'Operarios Directos',
        'Jefes de Turno': 'Supervisores Directos',
        'Supervisores Produccion': 'Supervisores Directos',
        'Operarios Indirectos (Frigorífico, Aseo, etc)': 'Personal Indirecto',
        'Supervisores Calidad': 'Personal Indirecto',
        'Monitores Calidad': 'Personal Indirecto',
        'Operadores Mantencion y Administracion Directo Planta (Contador': 'Personal Indirecto',
        'Tesorero': 'Personal Indirecto',
        'Asistente Contabilidad': 'Personal Indirecto',
        'Jefe RRHH': 'Personal Indirecto',
        'Jefe de Adquisicion': 'Personal Indirecto',
        'Prevencion de Riesgo': 'Personal Indirecto',
        'Control de Gestion': 'Personal Indirecto',
            'Otro cargo que su trabajo esté asociado directamente con Planta':'Otros Indirectos', 
    'Operarios Indirectos (Frigorifico, Aseo, etc)':'Personal Indirecto',
    'Otro cargo que su trabajo esta asociado directamente con Planta':'Personal Indirecto'
    }

    dotacion['Grupo'] = dotacion.Cargo.apply(lambda c: dict_cargos[c])
    dotacion['Tipo'] = dotacion.Grupo.apply(lambda g: 'Personal Directo' if g != 'Personal Indirecto' else g)
    dotacion['Personal Planta'] = dotacion['Personal Planta Contrato Plazo Fijo'] + dotacion['Personal Planta Contrato Plazo Indefinido']

    tabla = dotacion[['Tipo','Mes','Personal Planta']].pivot_table(index = 'Tipo', columns = 'Mes', aggfunc = np.sum)

    tabla.loc[('Total personal')] = tabla.sum(axis=0)
    columnas = [dict_meses[int(i[1])] if i[0] == 'Personal Planta' else i[0] for i in list(tabla.columns)]
    tabla.columns = columnas
    
    ton_neta = ingresos_mmpp[(ingresos_mmpp.Tipo == 'MP Neta')].pivot_table(index='Tipo', columns = 'Mes', values='Valor', aggfunc=np.sum)
    ton_neta.columns = [dict_meses[c] for c in list(ton_neta.columns)]
    tabla.loc['Ton mmpp Neta / Total Personal'] = ''
    for i in list(tabla.columns):
      if i in ton_neta.columns:
        tabla.loc[['Ton mmpp Neta / Total Personal'],[i]] = ton_neta.loc['MP Neta'].loc[i]/(tabla.loc['Total personal'].loc[i])
    
    ton_PT = producto.pivot_table(index='Ano', columns = 'Mes', values='Valor', aggfunc=np.sum)
    ton_PT.columns = [dict_meses[c] for c in list(ton_PT.columns)]
    tabla.loc['Ton Neta PT / Total Personal'] = ''
    for i in list(tabla.columns):
      if i in ton_PT.columns:
        tabla.loc[['Ton Neta PT / Total Personal'],[i]] = ton_PT.loc[int(ano)].loc[i]/(tabla.loc['Total personal'].loc[i])
    # 
    # tabla.loc['Ton Neta PT / Total Personal'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Total personal'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.fillna(0, inplace=True)
    # tabla.loc['Ton Neta PT / Total Personal'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Total personal'].iloc[i]) for i in range(tabla.shape[1])]

    # En este caso, vamos a botar columnas completas en cada caso donde se observen menos de 3 industrias por caso

    contador = dotacion.groupby('Mes').Empresa.nunique()

    tabla.loc['N Empresas'] = contador.values
    backup = tabla.copy()
    for col in tabla.columns:
      if tabla[col].loc["N Empresas"] < 3:
        tabla.drop([col], axis=1, inplace=True)
    tabla.fillna(0, inplace=True)
    tabla['Promedio'] = tabla.mean(axis=1)

    return tabla
  
def resumen_dotacion_empresa(empresa = 'Data Mussel', ano = 2021):
    import pandas as pd
    import numpy as np
    ### Carga Ingresos
    
    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv")
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)

    idx = pd.IndexSlice

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    # Agrupacion indicadores

    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])

    idx = pd.IndexSlice

    ##### Carga Producto Terminado
    
    producto = pd.read_csv("Procesos_Producto_Consolidado.csv") 
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)

    producto.head()

    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    ###### DOTACION 
    dotacion = pd.read_csv("Procesos_Dotacion_Consolidado.csv")
    dotacion = dotacion[dotacion['Ano'] == int(ano)]
    dotacion.Mes = dotacion.Mes.apply(lambda m: pd.to_numeric(m, errors='coerce'))
    dotacion.Mes.fillna(0, inplace=True)
    
    dict_meses = {
    1:'Ene',
    2:'Feb',
    3:'Mar',
    4:'Abr',
    5:'May',
    6:'Jun',
    7:'Jul',
    8:'Ago',
    9:'Sep',
    10:'Oct',
    11:'Nov',
    12:'Dic',
    }

    dict_cargos =  {
        'Operarios directos (produccion)': 'Operarios Directos',
        'Jefes de Turno': 'Supervisores Directos',
        'Supervisores Produccion': 'Supervisores Directos',
        'Operarios Indirectos (Frigorífico, Aseo, etc)': 'Personal Indirecto',
        'Supervisores Calidad': 'Personal Indirecto',
        'Monitores Calidad': 'Personal Indirecto',
        'Operadores Mantencion y Administracion Directo Planta (Contador': 'Personal Indirecto',
        'Tesorero': 'Personal Indirecto',
        'Asistente Contabilidad': 'Personal Indirecto',
        'Jefe RRHH': 'Personal Indirecto',
        'Jefe de Adquisicion': 'Personal Indirecto',
        'Prevencion de Riesgo': 'Personal Indirecto',
        'Control de Gestion': 'Personal Indirecto',
          'Otro cargo que su trabajo esté asociado directamente con Planta':'Otros Indirectos', 
    'Operarios Indirectos (Frigorifico, Aseo, etc)':'Personal Indirecto',
    'Otro cargo que su trabajo esta asociado directamente con Planta':'Personal Indirecto'
    }

    dotacion = dotacion.loc[dotacion.Empresa == empresa]

    dotacion['Grupo'] = dotacion.Cargo.apply(lambda c: dict_cargos[c])
    dotacion['Tipo'] = dotacion.Grupo.apply(lambda g: 'Personal Directo' if g != 'Personal Indirecto' else g)
    dotacion['Personal Planta'] = dotacion['Personal Planta Contrato Plazo Fijo'] + dotacion['Personal Planta Contrato Plazo Indefinido']

    tabla = dotacion[['Tipo','Mes','Personal Planta']].pivot_table(index = 'Tipo', columns = 'Mes', aggfunc = np.sum)
    tabla.loc[('Total personal')] = tabla.sum(axis=0)
    columnas = [dict_meses[int(i[1])] if i[0] == 'Personal Planta' else i[0] for i in list(tabla.columns)]
    tabla.columns = columnas

    # Agregar columnas Toneladas MMPP Neta

    ton_neta = ingresos_mmpp[(ingresos_mmpp.Tipo == 'MP Neta') & (ingresos_mmpp.Empresa==empresa)].pivot_table(index='Empresa', columns = 'Mes', values='Valor', aggfunc=np.sum)
    ton_neta.columns = [dict_meses[c] for c in list(ton_neta.columns)]
    tabla.loc['Ton mmpp Neta / Total Personal'] = ''
    for i in list(tabla.columns):
      if i in ton_neta.columns:
        tabla.loc[['Ton mmpp Neta / Total Personal'],[i]] = ton_neta.loc[empresa].loc[i]/(tabla.loc['Total personal'].loc[i])
    
    ton_PT = producto.loc[producto.Empresa == empresa].pivot_table(index='Empresa', columns = 'Mes', values='Valor', aggfunc=np.sum)
    ton_PT.columns = [dict_meses[c] for c in list(ton_PT.columns)]
    tabla.loc['Ton Neta PT / Total Personal'] = ''
    for i in list(tabla.columns):
      if i in ton_PT.columns:
        tabla.loc[['Ton Neta PT / Total Personal'],[i]] = ton_PT.loc[empresa].loc[i]/(tabla.loc['Total personal'].loc[i])
    # 
    # tabla.loc['Ton Neta PT / Total Personal'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Total personal'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.fillna(0, inplace=True)
    tabla['Promedio'] = tabla.mean(axis=1)

    return tabla

def resumen_dotacion_otras(empresa = 'Data Mussel',ano = 2020):
    import pandas as pd
    import numpy as np
    
    ### Carga Ingresos
    
    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv")
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)

    idx = pd.IndexSlice

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    # Agrupacion indicadores

    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])

    idx = pd.IndexSlice

    ##### Carga Producto Terminado
    
    producto = pd.read_csv("Procesos_Producto_Consolidado.csv") 
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)

    producto.head()

    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]

    ###### DOTACION 
    dotacion = pd.read_csv("Procesos_Dotacion_Consolidado.csv")
    dotacion = dotacion[dotacion['Ano'] == int(ano)]
    dotacion.Mes = dotacion.Mes.apply(lambda m: pd.to_numeric(m, errors='coerce'))
    dotacion.Mes.fillna(0, inplace=True)
    
    dict_meses = {
    1:'Ene',
    2:'Feb',
    3:'Mar',
    4:'Abr',
    5:'May',
    6:'Jun',
    7:'Jul',
    8:'Ago',
    9:'Sep',
    10:'Oct',
    11:'Nov',
    12:'Dic',
    2021: '2021'
    }

    dict_cargos =  {
        'Operarios directos (produccion)': 'Operarios Directos',
        'Jefes de Turno': 'Supervisores Directos',
        'Supervisores Produccion': 'Supervisores Directos',
        'Operarios Indirectos (Frigorífico, Aseo, etc)': 'Personal Indirecto',
        'Supervisores Calidad': 'Personal Indirecto',
        'Monitores Calidad': 'Personal Indirecto',
        'Operadores Mantencion y Administracion Directo Planta (Contador': 'Personal Indirecto',
        'Tesorero': 'Personal Indirecto',
        'Asistente Contabilidad': 'Personal Indirecto',
        'Jefe RRHH': 'Personal Indirecto',
        'Jefe de Adquisicion': 'Personal Indirecto',
        'Prevencion de Riesgo': 'Personal Indirecto',
        'Control de Gestion': 'Personal Indirecto',
            'Otro cargo que su trabajo esté asociado directamente con Planta':'Otros Indirectos', 
    'Operarios Indirectos (Frigorifico, Aseo, etc)':'Personal Indirecto',
    'Otro cargo que su trabajo esta asociado directamente con Planta':'Personal Indirecto'
    }

    dotacion = dotacion.loc[dotacion.Empresa != empresa]

    dotacion['Grupo'] = dotacion.Cargo.apply(lambda c: dict_cargos[c])
    dotacion['Tipo'] = dotacion.Grupo.apply(lambda g: 'Personal Directo' if g != 'Personal Indirecto' else g)
    dotacion['Personal Planta'] = dotacion['Personal Planta Contrato Plazo Fijo'] + dotacion['Personal Planta Contrato Plazo Indefinido']

    tabla = dotacion[['Tipo','Mes','Personal Planta']].pivot_table(index = 'Tipo', columns = 'Mes', aggfunc = np.sum)
    tabla.loc[('Total personal')] = tabla.sum(axis=0)
    columnas = [dict_meses[int(i[1])] if i[0] == 'Personal Planta' else i[0] for i in list(tabla.columns)]
    tabla.columns = columnas
    # Agregar columnas Toneladas MMPP Neta/Total Personal para la Industria

    ton_neta = ingresos_mmpp[(ingresos_mmpp.Tipo == 'MP Neta') & (ingresos_mmpp.Empresa!=empresa)].pivot_table(index='Tipo', columns = 'Mes', values='Valor', aggfunc=np.sum)
    ton_neta.columns = [dict_meses[c] for c in list(ton_neta.columns)]
    tabla.loc['Ton mmpp Neta / Total Personal'] = ''
    for i in list(tabla.columns):
      if i in ton_neta.columns:
        tabla.loc[['Ton mmpp Neta / Total Personal'],[i]] = ton_neta.loc['MP Neta'].loc[i]/(tabla.loc['Total personal'].loc[i])
    
    ton_PT = producto.loc[producto.Empresa != empresa].pivot_table(index='Ano', columns = 'Mes', values='Valor', aggfunc=np.sum)
    ton_PT.columns = [dict_meses[c] for c in list(ton_PT.columns)]
    tabla.loc['Ton Neta PT / Total Personal'] = ''
    for i in list(tabla.columns):
      if i in ton_PT.columns:
        tabla.loc[['Ton Neta PT / Total Personal'],[i]] = ton_PT.loc[int(ano)].loc[i]/(tabla.loc['Total personal'].loc[i])
    # 
    # tabla.loc['Ton Neta PT / Total Personal'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Total personal'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.fillna(0, inplace=True)
    
    # En este caso, vamos a botar columnas completas en cada caso donde se observen menos de 3 industrias por caso

    contador = dotacion.groupby('Mes').Empresa.nunique()

    tabla.loc['N Empresas'] = contador.values

    for col in tabla.columns:
        if tabla[col].loc["N Empresas"] < 3:
            tabla.drop([col], axis=1, inplace=True)
    tabla.fillna(0, inplace=True)
    tabla['Promedio'] = tabla.mean(axis=1)

    return tabla

def detalle_dotacion_mensual(empresa = 'Manuelita', mes= 'Ene',ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice
    
    #Cambiar a numero
    dict_mes_num = {
        'Ene':1,
        'Feb':2,
        'Mar':3,
        'Abr':4,
        'May':5,
        'Jun':6,
        'Jul':7,
        'Ago':8,
        'Sep':9,
        'Oct':10,
        'Nov':11,
        'Dic':12
    }

    mes = dict_mes_num[mes]

    dotacion = pd.read_csv("Procesos_Dotacion_Consolidado.csv")
    dotacion = dotacion[dotacion['Ano'] == int(ano)]
    dotacion.Mes = dotacion.Mes.apply(lambda m: pd.to_numeric(m, errors='coerce'))
    dotacion.Mes.fillna(0, inplace=True)
    dotacion.head()

    dict_cargos =  {
        'Operarios directos (produccion)': 'Operarios Directos',
        'Jefes de Turno': 'Supervisores Directos',
        'Supervisores Produccion': 'Supervisores Directos',
        'Operarios Indirectos (Frigorífico, Aseo, etc)': 'Personal Indirecto',
        'Supervisores Calidad': 'Personal Indirecto',
        'Monitores Calidad': 'Personal Indirecto',
        'Operadores Mantencion y Administracion Directo Planta (Contador': 'Personal Indirecto',
        'Tesorero': 'Personal Indirecto',
        'Asistente Contabilidad': 'Personal Indirecto',
        'Jefe RRHH': 'Personal Indirecto',
        'Jefe de Adquisicion': 'Personal Indirecto',
        'Prevencion de Riesgo': 'Personal Indirecto',
        'Control de Gestion': 'Personal Indirecto',
        'Otro cargo que su trabajo esté asociado directamente con Planta': 'Personal Indirecto',
        'Operarios Indirectos (Frigorifico, Aseo, etc)':'Personal Indirecto',
        'Otro cargo que su trabajo esta asociado directamente con Planta':'Personal Indirecto'
    }

    dotacion['Grupo'] = dotacion.Cargo.apply(lambda c: dict_cargos[c])
    dotacion['Tipo'] = dotacion.Grupo.apply(lambda g: 'Personal Directo' if g != 'Personal Indirecto' else g)
    dotacion['Personal Planta'] = dotacion['Personal Planta Contrato Plazo Fijo'] + dotacion['Personal Planta Contrato Plazo Indefinido']

    dot_mes = dotacion[dotacion.Mes == mes]

    tabla = dot_mes.groupby('Tipo')['Personal Planta'].agg([('Total', sum),('Promedio', np.mean)])
    tabla['%'] = [tabla.Total[i]/tabla.Total.sum()*100 for i in range(len(tabla.Total))]
    tabla.loc['Total Personal'] = [tabla.Total.sum(), tabla.Promedio.mean(),np.nan]

    # Personal directo

    tabla_d = dot_mes[dot_mes.Tipo == 'Personal Directo'][['Personal Planta Contrato Plazo Fijo','Personal Planta Contrato Plazo Indefinido']].agg([sum, np.mean])
    tabla_d = tabla_d.T
    tabla_d.index = ['Contrato temporada', 'Contrato Plazo Definido']
    tabla_d.columns = ['Total', 'Promedio']
    tabla_d['%'] = [tabla_d.Total[i]/tabla_d.Total.sum()*100 for i in range(len(tabla_d.Total))]

    tabla_d.loc['Total Personal Directo'] = tabla_d.sum()

    # Personal Indirecto 

    tabla_ind = dot_mes[dot_mes.Tipo == 'Personal Indirecto'][['Personal Planta Contrato Plazo Fijo','Personal Planta Contrato Plazo Indefinido']].agg([sum, np.mean])
    tabla_ind = tabla_ind.T
    tabla_ind.index = ['Contrato temporada', 'Contrato Plazo Definido']
    tabla_ind.columns = ['Total', 'Promedio']
    tabla_ind['%'] = [tabla_ind.Total[i]/tabla_ind.Total.sum()*100 for i in range(len(tabla_ind.Total))]

    tabla_ind.loc['Total Personal Indirecto'] = tabla_ind.sum()

    # Tabla industria mensual
    tabla_industria = pd.concat([tabla, tabla_d, tabla_ind], axis=0)
    tabla_industria.fillna("", inplace = True)

    ##### EMPRESA 

    dot_mes = dotacion[(dotacion.Mes == mes) & (dotacion.Empresa == empresa)]

    tabla = dot_mes.groupby('Tipo')['Personal Planta'].agg([('Total', sum),('Promedio', np.mean)])
    tabla['%'] = [tabla.Total[i]/tabla.Total.sum()*100 for i in range(len(tabla.Total))]
    tabla.loc['Total Personal'] = [tabla.Total.sum(), tabla.Promedio.mean(),np.nan ]

    # Personal directo

    tabla_d = dot_mes[dot_mes.Tipo == 'Personal Directo'][['Personal Planta Contrato Plazo Fijo','Personal Planta Contrato Plazo Indefinido']].agg([sum, np.mean])
    tabla_d = tabla_d.T
    tabla_d.index = ['Contrato temporada', 'Contrato Plazo Definido']
    tabla_d.columns = ['Total', 'Promedio']
    tabla_d['%'] = [tabla_d.Total[i]/tabla_d.Total.sum()*100 for i in range(len(tabla_d.Total))]

    tabla_d.loc['Total Personal Directo'] = tabla_d.sum()

    # Personal Indirecto 

    tabla_ind = dot_mes[dot_mes.Tipo == 'Personal Indirecto'][['Personal Planta Contrato Plazo Fijo','Personal Planta Contrato Plazo Indefinido']].agg([sum, np.mean])
    tabla_ind = tabla_ind.T
    tabla_ind.index = ['Contrato temporada', 'Contrato Plazo Definido']
    tabla_ind.columns = ['Total', 'Promedio']
    tabla_ind['%'] = [tabla_ind.Total[i]/tabla_ind.Total.sum()*100 for i in range(len(tabla_ind.Total))]

    tabla_ind.loc['Total Personal Indirecto'] = tabla_ind.sum()

    # Tabla industria mensual
    tabla_empresa = pd.concat([tabla, tabla_d, tabla_ind], axis=0)
    tabla_empresa.fillna("", inplace = True)

    ##### OTRAS INDUSTRIAS

    dot_mes = dotacion[(dotacion.Mes == mes) & (dotacion.Empresa != empresa)]

    tabla = dot_mes.groupby('Tipo')['Personal Planta'].agg([('Total', sum),('Promedio', np.mean)])
    tabla['%'] = [tabla.Total[i]/tabla.Total.sum()*100 for i in range(len(tabla.Total))]
    tabla.loc['Total Personal'] = [tabla.Total.sum(), tabla.Promedio.mean(),np.nan]

    # Personal directo

    tabla_d = dot_mes[dot_mes.Tipo == 'Personal Directo'][['Personal Planta Contrato Plazo Fijo','Personal Planta Contrato Plazo Indefinido']].agg([sum, np.mean])
    tabla_d = tabla_d.T
    tabla_d.index = ['Contrato temporada', 'Contrato Plazo Definido']
    tabla_d.columns = ['Total', 'Promedio']
    tabla_d['%'] = [tabla_d.Total[i]/tabla_d.Total.sum()*100 for i in range(len(tabla_d.Total))]

    tabla_d.loc['Total Personal Directo'] = tabla_d.sum()

    # Personal Indirecto 

    tabla_ind = dot_mes[dot_mes.Tipo == 'Personal Indirecto'][['Personal Planta Contrato Plazo Fijo','Personal Planta Contrato Plazo Indefinido']].agg([sum, np.mean])
    tabla_ind = tabla_ind.T
    tabla_ind.index = ['Contrato temporada', 'Contrato Plazo Definido']
    tabla_ind.columns = ['Total', 'Promedio']
    tabla_ind['%'] = [tabla_ind.Total[i]/tabla_ind.Total.sum()*100 for i in range(len(tabla_ind.Total))]

    tabla_ind.loc['Total Personal Indirecto'] = tabla_ind.sum()

    # Tabla industria mensual
    tabla_otras = pd.concat([tabla, tabla_d, tabla_ind], axis=0)
    tabla_otras.fillna("", inplace = True)

    #### Union de las tablas

    contador = dot_mes.Empresa.nunique()
    if contador <3:
        tabla_completa = tabla_empresa.copy()
    else:
        tabla_completa = pd.concat([tabla_industria, tabla_empresa, tabla_otras], axis=1)
        tabla_completa.loc["N Empresas"] = contador

    tabla_completa = tabla_completa.reset_index(level=None, drop=False).fillna("")

    return tabla_completa

def detalle_dotacion_areas(empresa = 'Data Mussel', mes = 'Ene',ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice
    
    #Cambiar a numero
    dict_mes_num = {
        'Ene':1,
        'Feb':2,
        'Mar':3,
        'Abr':4,
        'May':5,
        'Jun':6,
        'Jul':7,
        'Ago':8,
        'Sep':9,
        'Oct':10,
        'Nov':11,
        'Dic':12
    }

    mes = dict_mes_num[mes]

    dotacion = pd.read_csv("Procesos_Dotacion_Consolidado.csv") 
    dotacion = dotacion[dotacion['Ano'] == int(ano)]
    dotacion.Mes = dotacion.Mes.apply(lambda m: pd.to_numeric(m, errors='coerce'))
    dotacion.Mes.fillna(0, inplace=True)
    dotacion.head()

    dict_cargos =  {
        'Operarios directos (produccion)': 'Operarios Directos',
        'Jefes de Turno': 'Supervisores Directos',
        'Supervisores Produccion': 'Supervisores Directos',
        'Operarios Indirectos (Frigorífico, Aseo, etc)': 'Personal Indirecto',
        'Supervisores Calidad': 'Personal Indirecto',
        'Monitores Calidad': 'Personal Indirecto',
        'Operadores Mantencion y Administracion Directo Planta (Contador': 'Personal Indirecto',
        'Tesorero': 'Personal Indirecto',
        'Asistente Contabilidad': 'Personal Indirecto',
        'Jefe RRHH': 'Personal Indirecto',
        'Jefe de Adquisicion': 'Personal Indirecto',
        'Prevencion de Riesgo': 'Personal Indirecto',
        'Control de Gestion': 'Personal Indirecto',
        'Otro cargo que su trabajo esté asociado directamente con Planta': 'Personal Indirecto',
        'Operarios Indirectos (Frigorifico, Aseo, etc)':'Personal Indirecto',
        'Otro cargo que su trabajo esta asociado directamente con Planta':'Personal Indirecto'
    }

    dotacion['Grupo'] = dotacion.Cargo.apply(lambda c: dict_cargos[c])
    dotacion['Tipo'] = dotacion.Grupo.apply(lambda g: 'Personal Directo' if g != 'Personal Indirecto' else g)
    dotacion['Personal Planta'] = dotacion['Personal Planta Contrato Plazo Fijo'] + dotacion['Personal Planta Contrato Plazo Indefinido']

    dict_cargos_tabla = {
    'Operarios directos (produccion)': 'Produccion Operarios',
    'Operarios Indirectos (Frigorífico, Aseo, etc)': 'Operarios Indirectos',
    'Jefes de Turno': 'Produccion Jefatura',
    'Supervisores Produccion': 'Produccion Jefatura',
    'Supervisores Calidad': 'Calidad',
    'Monitores Calidad': 'Calidad',
    'Operadores Mantencion y Administracion Directo Planta (Contador':'Otros Indirectos',
    'Tesorero':'Otros Indirectos',
    'Asistente Contabilidad':'Otros Indirectos',
    'Jefe RRHH':'Otros Indirectos',
    'Jefe de Adquisicion':'Otros Indirectos',
    'Prevencion de Riesgo':'Otros Indirectos',
    'Control de Gestion':'Otros Indirectos',
    'Otro cargo que su trabajo esté asociado directamente con Planta':'Otros Indirectos', 
    'Operarios Indirectos (Frigorifico, Aseo, etc)':'Personal Indirecto',
    'Otro cargo que su trabajo esta asociado directamente con Planta':'Personal Indirecto'
    }

    dotacion['Area'] = [ dict_cargos_tabla[ele] for ele in dotacion.Cargo]

    #Contrato temporada
    tabla_ct = dotacion[dotacion.Mes == mes].pivot_table(index = 'Area', values = ['Personal Planta Contrato Plazo Fijo'], aggfunc=np.sum)
    if tabla_ct.shape[1] > 0:
      tabla_ct.columns = ['Contrato temporada']
      tabla_ct['%'] = [tabla_ct['Contrato temporada'][i]/tabla_ct['Contrato temporada'].sum() for i in range(len(tabla_ct['Contrato temporada']))]
      tabla_ct.loc['Total personal'] = tabla_ct.sum()
    else:
      tabla_ct = pd.DataFrame()
    

    #Contrato indefinido
    tabla_pi = dotacion[dotacion.Mes == mes].pivot_table(index = 'Area', values = ['Personal Planta Contrato Plazo Indefinido'], aggfunc=np.sum)
    tabla_pi.columns = ['Contrato indefinido']
    tabla_pi['%'] = [tabla_pi['Contrato indefinido'][i]/tabla_pi['Contrato indefinido'].sum() for i in range(len(tabla_pi['Contrato indefinido']))]
    tabla_pi.loc['Total personal'] = tabla_pi.sum()

    #Personal planta
    tabla_p = dotacion[dotacion.Mes == mes].pivot_table(index = 'Area', values = ['Personal Planta'], aggfunc=np.sum)
    tabla_p.columns = ['Total Planta']
    tabla_p['%'] = [tabla_p['Total Planta'][i]/tabla_p['Total Planta'].sum() for i in range(len(tabla_p['Total Planta']))]
    tabla_p.loc['Total personal'] = tabla_p.sum()

    tabla_personal_industria = pd.concat([tabla_ct, tabla_pi, tabla_p], axis=1)

    #### EMPRESA

    #Contrato temporada
    tabla_ct = dotacion[(dotacion.Mes == mes) & (dotacion.Empresa == empresa)].pivot_table(index = 'Area', values = ['Personal Planta Contrato Plazo Fijo'], aggfunc=np.sum)
    tabla_ct.columns = ['Contrato temporada']
    tabla_ct['%'] = [tabla_ct['Contrato temporada'][i]/tabla_ct['Contrato temporada'].sum() for i in range(len(tabla_ct['Contrato temporada']))]
    tabla_ct.loc['Total personal'] = tabla_ct.sum()

    #Contrato indefinido
    tabla_pi = dotacion[dotacion.Mes == mes & (dotacion.Empresa == empresa)].pivot_table(index = 'Area', values = ['Personal Planta Contrato Plazo Indefinido'], aggfunc=np.sum)
    tabla_pi.columns = ['Contrato indefinido']
    tabla_pi['%'] = [tabla_pi['Contrato indefinido'][i]/tabla_pi['Contrato indefinido'].sum() for i in range(len(tabla_pi['Contrato indefinido']))]
    tabla_pi.loc['Total personal'] = tabla_pi['Contrato indefinido'].sum()

    #Personal Planta
    tabla_p = dotacion[dotacion.Mes == mes & (dotacion.Empresa == empresa)].pivot_table(index = 'Area', values = ['Personal Planta'], aggfunc=np.sum)
    tabla_p.columns = ['Total Planta']
    tabla_p['%'] = [tabla_p['Total Planta'][i]/tabla_p['Total Planta'].sum() for i in range(len(tabla_p['Total Planta']))]
    tabla_p.loc['Total personal'] = tabla_p['Total Planta'].sum()

    tabla_personal_empresa = pd.concat([tabla_ct, tabla_pi, tabla_p], axis=1)

    #### OTRAS EMPRESAS

    #Contrato temporada
    tabla_ct = dotacion[(dotacion.Mes == mes) & (dotacion.Empresa != empresa)].pivot_table(index = 'Area', values = ['Personal Planta Contrato Plazo Fijo'], aggfunc=np.sum)
    tabla_ct.columns = ['Contrato temporada']
    tabla_ct['%'] = [tabla_ct['Contrato temporada'][i]/tabla_ct['Contrato temporada'].sum() for i in range(len(tabla_ct['Contrato temporada']))]
    tabla_ct.loc['Total personal'] = tabla_ct['Contrato temporada'].sum()

    #Contrato indefinido
    tabla_pi = dotacion[(dotacion.Mes == mes) & (dotacion.Empresa != empresa)].pivot_table(index = 'Area', values = ['Personal Planta Contrato Plazo Indefinido'], aggfunc=np.sum)
    tabla_pi.columns = ['Contrato indefinido']
    tabla_pi['%'] = [tabla_pi['Contrato indefinido'][i]/tabla_pi['Contrato indefinido'].sum() for i in range(len(tabla_pi['Contrato indefinido']))]
    tabla_pi.loc['Total personal'] = tabla_pi.sum()

    #Personal Planta
    tabla_p = dotacion[(dotacion.Mes == mes) & (dotacion.Empresa != empresa)].pivot_table(index = 'Area', values = ['Personal Planta'], aggfunc=np.sum)
    tabla_p.columns = ['Total Planta']
    tabla_p['%'] = [tabla_p ['Total Planta'][i]/tabla_p['Total Planta'].sum() for i in range(len(tabla_p['Total Planta']))]
    tabla_p.loc['Total personal'] = tabla_p.sum()

    tabla_personal_otras = pd.concat([tabla_ct, tabla_pi, tabla_p], axis=1)

    #### Union de las tablas
    dot_mes = dotacion[dotacion.Mes == mes]
    contador = dot_mes.Empresa.nunique()
    if contador <3:
        tabla_detalle_dot = tabla_personal_empresa.copy()
    else:
        tabla_detalle_dot = pd.concat([tabla_personal_industria, tabla_personal_empresa, tabla_personal_otras], axis=1)
        tabla_detalle_dot.loc["N Empresas"] = contador
        
    return tabla_detalle_dot

def detalle_dotacion_mes(empresa = 'Data Mussel', mes = 'Ene',ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice
    
    ingresos_mmpp = pd.read_csv("Procesos_Ingresos_Consolidado.csv") 
    ingresos_mmpp = ingresos_mmpp[ingresos_mmpp['Ano'] == int(ano)]
    ingresos_mmpp['Valor'] = ingresos_mmpp['Valor'].apply(lambda p: pd.to_numeric(p, errors='coerce'))
    ingresos_mmpp['Valor'].fillna(0, inplace=True)
    ingresos_mmpp.reset_index(drop=True, inplace=True)

    idx = pd.IndexSlice

    # Kilos a toneladas
    ingresos_mmpp['Valor'] = [ingresos_mmpp.Valor.iloc[i]/1000 if ingresos_mmpp['Unidad de medida'].iloc[i] == 'Kilos' else ingresos_mmpp.Valor.iloc[i]  for i in ingresos_mmpp.index]
    ingresos_mmpp['Unidad de medida'] = ingresos_mmpp['Unidad de medida'].apply(lambda u: 'Ton' if u == 'Kilos' else u)

    # Agrupacion indicadores

    dict_indicadores_tipo = {
        'Ingreso Materia Bruta Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Propia':'MP Neta',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MP Neta',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MP Bruta',
        'Ingreso Materia Prima Neta Propia':'MP Neta',
        'Ingreso Materia Prima Terceros':'MP Bruta',
        'Ingreso Materia Prima Neta Terceros':'MP Neta',
        'Ingreso Materia Prima Bruta Propia':'MP Bruta',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Tipo'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_tipo[i])

    dict_indicadores_propiedad = {
        'Ingreso Materia Bruta Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta o Pagada Propia':'MPN Propia',
        'Unidades por kilo mmpp propia*': "No aplica",
        'Ingreso Materia Prima Bruta Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta o Pagada Terceros':'MPN Terceros',
        'Unidades por kilo mmpp terceros*': "No aplica", 
        'Ingreso Materia Prima Propia':'MPB Propia',
        'Ingreso Materia Prima Neta Propia':'MPN Propia',
        'Ingreso Materia Prima Terceros':'MPB Terceros',
        'Ingreso Materia Prima Neta Terceros':'MPN Terceros',
        'Ingreso Materia Prima Bruta Propia':'MPB Propia',
        'rechazo propio': "No aplica",
        'rechazo terceros': "No aplica"
    }

    ingresos_mmpp['Propiedad'] = ingresos_mmpp.Indicador.apply(lambda i: dict_indicadores_propiedad[i])

    idx = pd.IndexSlice

    ##### Carga Producto Terminado
    
    producto = pd.read_csv("Procesos_Producto_Consolidado.csv") 
    producto = producto[producto['Ano'] == int(ano)]
    producto['Valor'] = producto['Valor'].apply(lambda v: pd.to_numeric(v, errors='coerce'))
    producto['Valor'].fillna(0, inplace=True)

    producto.head()

    producto.Valor = [producto['Valor'].iloc[i]/1000 if producto['Unidad de medida'].iloc[i] == 'Kilos' else producto['Valor'].iloc[i] for i in range(len(producto.Valor))]
    
    #Cambiar a numero
    dict_mes_num = {
        'Ene':1,
        'Feb':2,
        'Mar':3,
        'Abr':4,
        'May':5,
        'Jun':6,
        'Jul':7,
        'Ago':8,
        'Sep':9,
        'Oct':10,
        'Nov':11,
        'Dic':12
    }

    mes = dict_mes_num[mes]

    # Detalle Indicadores Dotacion para el mes
    dotacion = pd.read_csv("Procesos_Dotacion_Consolidado.csv") 
    dotacion = dotacion[dotacion['Ano'] == int(ano)]
    
    dotacion.Mes = dotacion.Mes.apply(lambda m: pd.to_numeric(m, errors='coerce'))
    dotacion.Mes.fillna(0, inplace=True)

    dict_cargos =  {
        'Operarios directos (produccion)': 'Operarios Directos',
        'Jefes de Turno': 'Supervisores Directos',
        'Supervisores Produccion': 'Supervisores Directos',
        'Operarios Indirectos (Frigorífico, Aseo, etc)': 'Personal Indirecto',
        'Supervisores Calidad': 'Personal Indirecto',
        'Monitores Calidad': 'Personal Indirecto',
        'Operadores Mantencion y Administracion Directo Planta (Contador': 'Personal Indirecto',
        'Tesorero': 'Personal Indirecto',
        'Asistente Contabilidad': 'Personal Indirecto',
        'Jefe RRHH': 'Personal Indirecto',
        'Jefe de Adquisicion': 'Personal Indirecto',
        'Prevencion de Riesgo': 'Personal Indirecto',
        'Control de Gestion': 'Personal Indirecto',
    'Otro cargo que su trabajo esté asociado directamente con Planta':'Otros Indirectos', 
    'Operarios Indirectos (Frigorifico, Aseo, etc)':'Personal Indirecto',
    'Otro cargo que su trabajo esta asociado directamente con Planta':'Personal Indirecto'
    }
    
    dict_meses = {
        1:'Ene',
        2:'Feb',
        3:'Mar',
        4:'Abr',
        5:'May',
        6:'Jun',
        7:'Jul',
        8:'Ago',
        9:'Sep',
        10:'Oct',
        11:'Nov',
        12:'Dic',
        2021: '2021'
    }

    dotacion['Grupo'] = dotacion.Cargo.apply(lambda c: dict_cargos[c])
    dotacion['Tipo'] = dotacion.Grupo.apply(lambda g: 'Personal Directo' if g != 'Personal Indirecto' else g)
    dotacion['Personal Planta'] = dotacion['Personal Planta Contrato Plazo Fijo'] + dotacion['Personal Planta Contrato Plazo Indefinido']

    tabla = dotacion[['Tipo','Mes','Personal Planta']].pivot_table(index = 'Tipo', columns = 'Mes', aggfunc = np.sum)
    tabla.loc[('Total personal')] = tabla.sum(axis=0)

    tabla.columns = [dict_meses[i[1]] if i[0] == 'Personal Planta' else i[0] for i in list(tabla.columns)]

    # Agregar columnas Toneladas MMPP Neta

    ton_neta = ingresos_mmpp[(ingresos_mmpp.Tipo == 'MP Neta')].pivot_table(index='Mes', values='Valor', aggfunc=np.sum)

    # Ver si faltan columnas
    for i in range(tabla.shape[1]):
        try:
            ton_neta.loc[i+1]
        except:
            ton_neta.loc[i+1] = 0


    tabla.loc["Ton mmpp Neta / Total Personal"] = [ton_neta.loc[i+1].loc['Valor']/(tabla.loc['Total personal'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc["Ton mmpp Neta / Personal Directo"] = [ton_neta.loc[i+1].loc['Valor']/(tabla.loc['Personal Directo'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc["Ton mmpp Neta / Personal Indirecto"] = [ton_neta.loc[i+1].loc['Valor']/(tabla.loc['Personal Indirecto'].iloc[i]) for i in range(tabla.shape[1])]


    ton_PT = producto.pivot_table(index='Mes', values='Valor', aggfunc=np.sum)

    # Ver si faltan columnas
    for i in range(tabla.shape[1]-1):
        try:
            ton_PT.loc[i+1]
        except:
            ton_PT.loc[i+1] = 0

    tabla.loc['Ton Neta PT / Total Personal'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Total personal'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc['Ton Neta PT / Personal Directo'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Personal Directo'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc['Ton Neta PT / Personal Indirecto'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Personal Indirecto'].iloc[i]) for i in range(tabla.shape[1])]

    tabla.loc['P. Indirecto / P. Directo'] = [(tabla.loc['Personal Indirecto'].iloc[i])/(tabla.loc['Personal Directo'].iloc[i]) for i in range(tabla.shape[1])]

    # Dropear las primeras dos filas
    tabla.drop(['Personal Directo', 'Personal Indirecto', 'Total personal'], axis=0, inplace=True)

    # Dejar solo último mes
    tabla_industria = tabla[dict_meses[mes]]
    tabla_industria.columns = ['Industria']

    #### DATOS EMPRESA

    # Detalle Indicadores Dotacion Empresa
    dotacion2 = dotacion[dotacion.Empresa == empresa]

    tabla = dotacion2[['Tipo','Mes','Personal Planta']].pivot_table(index = 'Tipo', columns = 'Mes', aggfunc = np.sum)
    tabla.loc[('Total personal')] = tabla.sum(axis=0)

    tabla.columns = [dict_meses[i[1]] if i[0] == 'Personal Planta' else i[0] for i in list(tabla.columns)]



    # Agregar columnas Toneladas MMPP Neta

    ton_neta = ingresos_mmpp[(ingresos_mmpp.Tipo == 'MP Neta') & (ingresos_mmpp.Empresa==empresa)].pivot_table(index='Mes', values='Valor', aggfunc=np.sum)

    # Ver si faltan columnas
    for i in range(tabla.shape[1]):
        try:
            ton_neta.loc[i+1]
        except:
            ton_neta.loc[i+1] = 0


    tabla.loc["Ton mmpp Neta / Total Personal"] = [ton_neta.loc[i+1].loc['Valor']/(tabla.loc['Total personal'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc["Ton mmpp Neta / Personal Directo"] = [ton_neta.loc[i+1].loc['Valor']/(tabla.loc['Personal Directo'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc["Ton mmpp Neta / Personal Indirecto"] = [ton_neta.loc[i+1].loc['Valor']/(tabla.loc['Personal Indirecto'].iloc[i]) for i in range(tabla.shape[1])]


    ton_PT = producto.loc[producto.Empresa == empresa].pivot_table(index='Mes', values='Valor', aggfunc=np.sum)

    # Ver si faltan columnas
    for i in range(tabla.shape[1]-1):
        try:
            ton_PT.loc[i+1]
        except:
            ton_PT.loc[i+1] = 0

    tabla.loc['Ton Neta PT / Total Personal'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Total personal'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc['Ton Neta PT / Personal Directo'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Personal Directo'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc['Ton Neta PT / Personal Indirecto'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Personal Indirecto'].iloc[i]) for i in range(tabla.shape[1])]

    tabla.loc['P. Indirecto / P. Directo'] = [(tabla.loc['Personal Indirecto'].iloc[i])/(tabla.loc['Personal Directo'].iloc[i]) for i in range(tabla.shape[1])]

    # Dropear las primeras dos filas
    tabla.drop(['Personal Directo', 'Personal Indirecto', 'Total personal'], axis=0, inplace=True)

    # Dejar solo último mes
    tabla_empresa = tabla[dict_meses[mes]]
    tabla_empresa.columns = [empresa]

    #### OTRAS EMPRESAS
    # Detalle Indicadores Dotacion Otras Empresas
    dotacion2 = dotacion[dotacion.Empresa != empresa]

    tabla = dotacion2[['Tipo','Mes','Personal Planta']].pivot_table(index = 'Tipo', columns = 'Mes', aggfunc = np.sum)
    tabla.loc[('Total personal')] = tabla.sum(axis=0)

    tabla.columns = [dict_meses[i[1]] if i[0] == 'Personal Planta' else i[0] for i in list(tabla.columns)]


    # Agregar columnas Toneladas MMPP Neta

    ton_neta = ingresos_mmpp[(ingresos_mmpp.Tipo == 'MP Neta') & (ingresos_mmpp.Empresa!=empresa)].pivot_table(index='Mes', values='Valor', aggfunc=np.sum)

    # Ver si faltan columnas
    for i in range(tabla.shape[1]):
        try:
            ton_neta.loc[i+1]
        except:
            ton_neta.loc[i+1] = 0


    tabla.loc["Ton mmpp Neta / Total Personal"] = [ton_neta.loc[i+1].loc['Valor']/(tabla.loc['Total personal'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc["Ton mmpp Neta / Personal Directo"] = [ton_neta.loc[i+1].loc['Valor']/(tabla.loc['Personal Directo'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc["Ton mmpp Neta / Personal Indirecto"] = [ton_neta.loc[i+1].loc['Valor']/(tabla.loc['Personal Indirecto'].iloc[i]) for i in range(tabla.shape[1])]


    ton_PT = producto.loc[producto.Empresa != empresa].pivot_table(index='Mes', values='Valor', aggfunc=np.sum)

    # Ver si faltan columnas
    for i in range(tabla.shape[1]):
        try:
            ton_PT.loc[i+1]
        except:
            ton_PT.loc[i+1] = 0

    tabla.loc['Ton Neta PT / Total Personal'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Total personal'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc['Ton Neta PT / Personal Directo'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Personal Directo'].iloc[i]) for i in range(tabla.shape[1])]
    tabla.loc['Ton Neta PT / Personal Indirecto'] = [ton_PT.loc[i+1].loc['Valor']/(tabla.loc['Personal Indirecto'].iloc[i]) for i in range(tabla.shape[1])]

    tabla.loc['P. Indirecto / P. Directo'] = [(tabla.loc['Personal Indirecto'].iloc[i])/(tabla.loc['Personal Directo'].iloc[i]) for i in range(tabla.shape[1])]

    # Dropear las primeras dos filas
    tabla.drop(['Personal Directo', 'Personal Indirecto', 'Total personal'], axis=0, inplace=True)

    # Dejar solo último mes
    tabla_otras = tabla[dict_meses[mes]]
    tabla_otras.columns = ['Otras empresas']

    #### JUNTAR TODO EN UN DF

    contador = dotacion[dotacion.Mes == mes].Empresa.nunique()
    if contador <3:
        tabla = tabla_empresa.copy()
    else:
        tabla = pd.concat([tabla_industria, tabla_empresa, tabla_otras], axis=1, ignore_index=True)
        tabla.loc["N Empresas"] = contador

    return tabla

def grafico_dotacion(ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice
    
    # Detalle Indicadores Dotacion para el mes
    dotacion = pd.read_csv("Procesos_Dotacion_Consolidado.csv") 
    dotacion = dotacion[dotacion['Ano'] == int(ano)]
    dotacion.Mes = dotacion.Mes.apply(lambda m: pd.to_numeric(m, errors='coerce'))
    dotacion.Mes.fillna(0, inplace=True)

    dict_cargos =  {
        'Operarios directos (produccion)': 'Operarios Directos',
        'Jefes de Turno': 'Supervisores Directos',
        'Supervisores Produccion': 'Supervisores Directos',
        'Operarios Indirectos (Frigorífico, Aseo, etc)': 'Personal Indirecto',
        'Supervisores Calidad': 'Personal Indirecto',
        'Monitores Calidad': 'Personal Indirecto',
        'Operadores Mantencion y Administracion Directo Planta (Contador': 'Personal Indirecto',
        'Tesorero': 'Personal Indirecto',
        'Asistente Contabilidad': 'Personal Indirecto',
        'Jefe RRHH': 'Personal Indirecto',
        'Jefe de Adquisicion': 'Personal Indirecto',
        'Prevencion de Riesgo': 'Personal Indirecto',
        'Control de Gestion': 'Personal Indirecto',
            'Otro cargo que su trabajo esté asociado directamente con Planta':'Otros Indirectos', 
    'Operarios Indirectos (Frigorifico, Aseo, etc)':'Personal Indirecto',
    'Otro cargo que su trabajo esta asociado directamente con Planta':'Personal Indirecto'
    }

    dotacion['Grupo'] = dotacion.Cargo.apply(lambda c: dict_cargos[c])
    dotacion['Tipo'] = dotacion.Grupo.apply(lambda g: 'Personal Directo' if g != 'Personal Indirecto' else g)
    dotacion['Personal Planta'] = dotacion['Personal Planta Contrato Plazo Fijo'] + dotacion['Personal Planta Contrato Plazo Indefinido']

    import plotly.express as px
    from plotly.subplots import make_subplots
    import plotly.graph_objects as go

    datos_w = dotacion.groupby(['Tipo','Mes'])['Personal Planta'].sum().unstack()
    datos_w = datos_w.T

    fig_industria = px.bar(datos_w, x=datos_w.index,
                y=['Personal Directo','Personal Indirecto'], title="Total Personal Planta",
                labels={'Mes':'Mes','value':'N° dotacion','variable':'Tipo'},
                color_discrete_map={"Personal Directo": "#00609C", "Personal Indirecto": "#F4D19F"},
                template="simple_white",
                width=700, 
                height=500)
                
    fig_industria.update_layout(
        font=dict(
            family="Arial",
            size=11
        )
    )

    import json
    import plotly
    
    fig_as_json = json.dumps(fig_industria, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json
  
def grafico_dotacion_contrato(ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice

    # Detalle Indicadores Dotacion para el mes
    dotacion = pd.read_csv("Procesos_Dotacion_Consolidado.csv")
    dotacion = dotacion[dotacion['Ano'] == int(ano)]
    dotacion.Mes = dotacion.Mes.apply(lambda m: pd.to_numeric(m, errors='coerce'))
    dotacion.Mes.fillna(0, inplace=True)

    dict_cargos =  {
        'Operarios directos (produccion)': 'Operarios Directos',
        'Jefes de Turno': 'Supervisores Directos',
        'Supervisores Produccion': 'Supervisores Directos',
        'Operarios Indirectos (Frigorífico, Aseo, etc)': 'Personal Indirecto',
        'Supervisores Calidad': 'Personal Indirecto',
        'Monitores Calidad': 'Personal Indirecto',
        'Operadores Mantencion y Administracion Directo Planta (Contador': 'Personal Indirecto',
        'Tesorero': 'Personal Indirecto',
        'Asistente Contabilidad': 'Personal Indirecto',
        'Jefe RRHH': 'Personal Indirecto',
        'Jefe de Adquisicion': 'Personal Indirecto',
        'Prevencion de Riesgo': 'Personal Indirecto',
        'Control de Gestion': 'Personal Indirecto',
            'Otro cargo que su trabajo esté asociado directamente con Planta':'Otros Indirectos', 
    'Operarios Indirectos (Frigorifico, Aseo, etc)':'Personal Indirecto',
    'Otro cargo que su trabajo esta asociado directamente con Planta':'Personal Indirecto'
    }

    dotacion['Grupo'] = dotacion.Cargo.apply(lambda c: dict_cargos[c])
    dotacion['Tipo'] = dotacion.Grupo.apply(lambda g: 'Personal Directo' if g != 'Personal Indirecto' else g)
    dotacion['Personal Planta'] = dotacion['Personal Planta Contrato Plazo Fijo'] + dotacion['Personal Planta Contrato Plazo Indefinido']

    # Tipo de Contrato
    import plotly.express as px
    from plotly.subplots import make_subplots
    import plotly.graph_objects as go

    datos_w = dotacion.groupby('Mes')[['Personal Planta Contrato Plazo Fijo','Personal Planta Contrato Plazo Indefinido']].sum()
    datos_w.columns = ['Contrato temporal','Contrato indefinido']

    fig_industria = px.bar(datos_w, x=datos_w.index,
                y=['Contrato temporal','Contrato indefinido'], title="Contrato Temporada vs Contrato Fijo",
                labels={'Mes':'Mes','value':'N° dotacion','variable':'Tipo'},
                color_discrete_map={"Contrato temporal": "#00609C", "Contrato indefinido": "#F4D19F"},
                template="simple_white",
                width=700, 
                height=500)
                
    fig_industria.update_layout(
        font=dict(
            family="Arial",
            size=11
        )
    )


    import json
    import plotly
    
    fig_as_json = json.dumps(fig_industria, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json
  
def precios_carne_granel(semana = 32,ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice

    precios = pd.read_csv('data_mussel_consolidado_precios.csv') 
    precios = precios[precios['Ano'] == int(ano)]
    
    # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas_single(df, thresh=3):
      aux = 0
      for i in range(len(df)):
        if (df.iloc[i-aux]['N Empresas'] < thresh) & (df.iloc[i-aux]['N Empresas'] != np.nan) :
          df = df.drop(df.index[i-aux])
          aux +=1
      return df

    tabla_precio = precios[precios.SEMANA == int(semana)].groupby(['PRODUCTO EQUIVALENTE','CALIBRE'])[['PRECIO EQUIV.','CANTIDAD','EMPRESA']].agg({'PRECIO EQUIV.': np.mean, 'CANTIDAD': np.sum, 'EMPRESA':lambda x: len(x.unique())})
    tabla_precio.columns = [ 'PRECIO EQUIV.',  'CANTIDAD','N Empresas']
    tabla_precio = remover_filas_single(tabla_precio)

    tabla_precio.dropna(axis=0, inplace=True)
    try:
        tabla_precio = tabla_precio.loc['CARNE GRANEL']
        tabla_precio['Distribucion'] = [tabla_precio.CANTIDAD.iloc[i]/tabla_precio.CANTIDAD.sum()*100 for i in range(len(tabla_precio.CANTIDAD))]
        tabla_precio.loc['Total'] = tabla_precio.sum()
        tabla_precio = tabla_precio.iloc[:,[0,1,3,2]]
    except: 
        tabla_precio = pd.DataFrame()
        contador = 0
        
    return tabla_precio

def precios_carne_retail(semana = 32,ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice

    precios = pd.read_csv('data_mussel_consolidado_precios.csv') 
    precios = precios[precios['Ano'] == int(ano)]

        # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas_single(df, thresh=3):
      aux = 0
      for i in range(len(df)):
          if (df.iloc[i-aux]['N Empresas'] < thresh) & (df.iloc[i-aux]['N Empresas'] != np.nan) :
            df = df.drop(df.index[i-aux])
            aux +=1
      return df

    tabla_precio = precios[precios.SEMANA == int(semana)].groupby(['PRODUCTO EQUIVALENTE','CALIBRE'])[['PRECIO EQUIV.','CANTIDAD', 'EMPRESA']].agg({'PRECIO EQUIV.': np.mean, 'CANTIDAD': np.sum,'EMPRESA': lambda x: len(x.unique()) })

    tabla_precio.columns = [ 'PRECIO EQUIV.',  'CANTIDAD','N Empresas']
    tabla_precio = remover_filas_single(tabla_precio)

    tabla_precio.dropna(axis=0, inplace=True)
    try:
      tabla_precio = tabla_precio.loc['CARNE RETAIL']
      tabla_precio['Distribucion'] = [tabla_precio.CANTIDAD.iloc[i]/tabla_precio.CANTIDAD.sum()*100 for i in range(len(tabla_precio.CANTIDAD))]
      tabla_precio.loc['Total'] = tabla_precio.sum()
    except:
      tabla_precio = pd.DataFrame()
      contador = 0
        
    return tabla_precio

def precios_entero(semana = 32,ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice

    precios = pd.read_csv('data_mussel_consolidado_precios.csv') 
    precios = precios[precios['Ano'] == int(ano)]

        # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas_single(df, thresh=3):
        aux = 0
        for i in range(len(df)):
            if (df.iloc[i-aux]['N Empresas'] < thresh) & (df.iloc[i-aux]['N Empresas'] != np.nan) :
                df = df.drop(df.index[i-aux])
                aux +=1
        return df

    tabla_precio = precios[precios.SEMANA == int(semana)].groupby(['PRODUCTO EQUIVALENTE','MERCADO'])[['PRECIO EQUIV.','CANTIDAD', 'EMPRESA']].agg({'PRECIO EQUIV.': np.mean, 'CANTIDAD': np.sum, 'EMPRESA': lambda x: len(x.unique())})

    tabla_precio.dropna(axis=0, inplace=True)
    tabla_precio.columns = [ 'PRECIO EQUIV.',  'CANTIDAD','N Empresas']
    tabla_precio = remover_filas_single(tabla_precio)

    try:
        tabla_precio = tabla_precio.loc['ENTERO SIN SALSA']
        tabla_precio['Distribucion'] = [tabla_precio.CANTIDAD.iloc[i]/tabla_precio.CANTIDAD.sum()*100 for i in range(len(tabla_precio.CANTIDAD))]
        tabla_precio.loc['Total'] = tabla_precio.sum()
        tabla_precio = tabla_precio.iloc[:,[0,1,3,2]]
    except:
        tabla_precio = pd.DataFrame()
        contador = 0

    return tabla_precio
  
def precios_entero_con_salsa(semana = 32,ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice

    precios = pd.read_csv('data_mussel_consolidado_precios.csv') 
    precios = precios[precios['Ano'] == int(ano)]

        # Funcion para remover filas con indicadores para menos de 3 empresas
    def remover_filas_single(df, thresh=3):
        aux = 0
        for i in range(len(df)):
            if (df.iloc[i-aux]['N Empresas'] < thresh) & (df.iloc[i-aux]['N Empresas'] != np.nan) :
                df = df.drop(df.index[i-aux])
                aux +=1
        return df

    tabla_precio = precios[precios.SEMANA == int(semana)].groupby(['PRODUCTO EQUIVALENTE','MERCADO'])[['PRECIO EQUIV.','CANTIDAD', 'EMPRESA']].agg({'PRECIO EQUIV.': np.mean, 'CANTIDAD': np.sum, 'EMPRESA':lambda x: len(x.unique())})

    tabla_precio.dropna(axis=0, inplace=True)
    tabla_precio.columns = [ 'PRECIO EQUIV.',  'CANTIDAD','N Empresas']
    tabla_precio = remover_filas_single(tabla_precio)
    try:
        tabla_precio = tabla_precio.loc['ENTERO CON SALSA']
        tabla_precio['Distribucion'] = [tabla_precio.CANTIDAD.iloc[i]/tabla_precio.CANTIDAD.sum()*100 for i in range(len(tabla_precio.CANTIDAD))]
        tabla_precio.loc['Total'] = tabla_precio.sum()
        contador = 1
    except:
        tabla_precio = pd.DataFrame()
        contador = 0

    return tabla_precio
  
def grafico_toneladas_semanas(ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice

    precios = pd.read_csv('data_mussel_consolidado_precios.csv') 
    precios = precios[precios['Ano'] == int(ano)]

    import plotly.express as px
    from plotly.subplots import make_subplots
    import plotly.graph_objects as go

    plot_data = precios.pivot_table(index='SEMANA', columns = 'PRODUCTO', values= 'CANTIDAD', aggfunc= np.sum)
    plot_data = plot_data/1000

    fig_industria = px.bar(plot_data, x=plot_data.index,
                y=['CARNE', 'ENTERO', 'MEDIA CONCHA'], title="Toneladas reportadas por semana - Semana ",
                labels={'Mes':'Semana','value':'Kilos','variable':'Tipo de producto'},
                color_discrete_map={"CARNE": "#00609C", "ENTERO": "#F4D19F","MEDIA CONCHA": "#C45130"},
                template="simple_white", 
                width=1000, 
                height=500)
                
    fig_industria.update_layout(
        barmode='group',
        font=dict(
            family="Arial",
            size=11
        )
    )

    import json
    import plotly
    
    fig_as_json = json.dumps(fig_industria, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json
  
def grafico_toneladas_semanas_carne_entero(ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice

    precios = pd.read_csv('data_mussel_consolidado_precios.csv') 
    precios = precios[precios['Ano'] == int(ano)]

    import plotly.express as px
    from plotly.subplots import make_subplots
    import plotly.graph_objects as go

    plot_data = precios.pivot_table(index='SEMANA', columns = 'PRODUCTO EQUIVALENTE', values= 'CANTIDAD', aggfunc= np.sum)
    plot_data = plot_data/1000
    plot_data = plot_data.loc[:,['CARNE RETAIL','ENTERO CON SALSA']]

    fig_industria = px.bar(plot_data, x=plot_data.index,
                y=['CARNE RETAIL', 'ENTERO CON SALSA'], title = "Toneladas reportadas por semana - Semana " ,labels={'SEMANA':'Semana','value':'Kilos','variable':'Tipo de producto'},
                color_discrete_map={"CARNE RETAIL": "#00609C", "ENTERO CON SALSA": "#F4D19F"},
                template="simple_white", 
                width=1000, 
                height=500)
                
    fig_industria.update_layout(
        barmode='group',
        font=dict(
            family="Arial",
            size=11
        )
    )

    import json
    import plotly
    
    fig_as_json = json.dumps(fig_industria, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json
  
def grafico_precios_semanas_carne_calibre(ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice

    precios = pd.read_csv('data_mussel_consolidado_precios.csv') 
    precios = precios[precios['Ano'] == int(ano)]

    import plotly.express as px
    from plotly.subplots import make_subplots
    import plotly.graph_objects as go

    plot_data = precios[(precios.PRODUCTO == 'CARNE') & (precios.MERCADO != 'Chile')].pivot_table(index='SEMANA', columns = 'CALIBRE', values= 'PRECIO EQUIV.', aggfunc= np.mean)
    plot_data = plot_data[['C100-200','C200-300','C300-500','C500-UP']]

    fig = px.line(plot_data, x=plot_data.index, y=['C100-200','C200-300','C300-500','C500-UP'],
                title = "Precios carne por calibre",            
                labels={'SEMANA':'Semana','value':'USD/Kg (CFR)','variable':'Calibre'},
                color_discrete_map={"C100-200": "#00609C", "C200-300": "#F4D19F","C300-500": "#C45130", "C500-UP":"#FFAA61"},
                template="simple_white", 
                width=1000, 
                height=500)
                
    fig.update_layout(
        font=dict(
            family="Arial",
            size=11
        )
    )

    import json
    import plotly
    
    fig_as_json = json.dumps(fig, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json
  
def grafico_precios_semanas_carne_retail(ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice

    precios = pd.read_csv('data_mussel_consolidado_precios.csv') 
    precios = precios[precios['Ano'] == int(ano)]

    import plotly.express as px
    from plotly.subplots import make_subplots
    import plotly.graph_objects as go

    plot_data = precios[(precios['PRODUCTO EQUIVALENTE'] == 'CARNE RETAIL') & (precios.MERCADO != 'Chile')].pivot_table(index='SEMANA', columns = 'CALIBRE', values= 'PRECIO EQUIV.', aggfunc= np.mean)

    fig = px.line(plot_data, x=plot_data.index, y=plot_data.columns,
                title = "Precios Carne Retail por calibre",            
                labels={'SEMANA':'Semana','value':'USD/Kg (CFR)','variable':'Calibre'},
                color_discrete_map={"C100-200": "#00609C", "C200-300": "#F4D19F","C300-500": "#C45130", "C500-UP":"#FFAA61"},
                template="simple_white", 
                width=1000, 
                height=500)
                
    fig.update_layout(
        font=dict(
            family="Arial",
            size=11
        )
    )

    import json
    import plotly
    
    fig_as_json = json.dumps(fig, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json
  
def grafico_toneladas_semanas_tipos(ano = 2021):
    import pandas as pd
    import numpy as np
    idx = pd.IndexSlice

    precios = pd.read_csv('data_mussel_consolidado_precios.csv') 
    precios = precios[precios['Ano'] == int(ano)]

    import plotly.express as px
    from plotly.subplots import make_subplots
    import plotly.graph_objects as go

    plot_data = precios.pivot_table(index='SEMANA', columns = 'PRODUCTO', values= 'CANTIDAD', aggfunc= np.sum)
    plot_data = plot_data/1000

    fig_industria = px.bar(plot_data, x=plot_data.index,
                y=['CARNE', 'ENTERO', 'MEDIA CONCHA'], title="Toneladas reportadas por semana - Semana "+str(semana),
                labels={'Mes':'Semana','value':'Kilos','variable':'Tipo de producto'},
                color_discrete_map={"CARNE": "#00609C", "ENTERO": "#F4D19F","MEDIA CONCHA": "#C45130"},
                template="simple_white", 
                width=1000, 
                height=500)
                
    fig_industria.update_layout(
        barmode='group',
        font=dict(
            family="Arial",
            size=11
        )
    )

    import json
    import plotly
    
    fig_as_json = json.dumps(fig, cls=plotly.utils.PlotlyJSONEncoder)
    
    return fig_as_json
