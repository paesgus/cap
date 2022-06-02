# cap
class Extratificacao():
    
    def __init__(self,n,df,class_col):
        self.n = n
        self.df = df
        self.class_col = class_col

    def _split(self, x):
        '''Recebe um numero x a ser dividido em n partes inteiras e retorna uma lista'''
        partes = []

        if(x < self.n):
            print("Há mais partes do que elementos")
            return None
        elif (x % self.n == 0):
            for i in range(self.n):
                partes.append(x//self.n)
        else:
            zp = self.n - (x % self.n)
            pp = x//self.n
            for i in range(self.n):
                if(i>= zp):
                    partes.append(pp + 1)
                else:
                    partes.append(pp)
        return partes

    def _preExtratificacao(self, parc_df):
        '''Extratifica um dataframe em n partes e retorna uma lista de dataframes'''
        count_reg = parc_df.shape[0]
        j = 0
        lista_dfs = []
        fator = self._split(count_reg)
        for i in range(self.n):
            sub_df = parc_df.iloc[j:fator[i]+j,:]
            lista_dfs.append(sub_df)
            j += fator[i]
        return lista_dfs
    
    def _printResultado(self, conjuntos):
        '''Apresenta os resultados obtidos de classes positivas e negativas por df extratificado'''
        positive = self.df[self.df[self.class_col]==1]
        negative = self.df[self.df[self.class_col]==0]
        print(f'''k = {self.n}, Dataset: {positive.shape[0]} positivas e {negative.shape[0]} negativas {int(100*positive.shape[0]/df.shape[0])}% x {int(100*negative.shape[0]/df.shape[0])}%''')
        x = 1
        for k in conjuntos:
            pos = k[k[self.class_col]==1]
            neg = k[k[self.class_col]==0]
            print(f'''FOLD {x}: Pos: {pos.shape[0]}, Neg: {neg.shape[0]}, Total:{pos.shape[0]+neg.shape[0]}, Proporção: {int(100*pos.shape[0]/k.shape[0])}% x {int(100*neg.shape[0]/k.shape[0])}%''')
            x+=1
            
    def extratificacao(self):
        '''Separa em dois dataframes utilizando a classe positiva e negativa e realiza 
        a juncao novamente apos a extratificacao'''
        positive_classes = self._preExtratificacao(self.df[self.df[self.class_col]==1])
        negative_classes = self._preExtratificacao(self.df[self.df[self.class_col]==0])
        dfs = [positive_classes[i].append(negative_classes[i]) for i in range(self.n)]
        self._printResultado(dfs)
        return dfs
