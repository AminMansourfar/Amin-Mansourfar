# Amin-Mansourfar
/*SQL- Hospital Report*/
--Anbar Stok Raporu
--IDARI
SELECT 
        hh.KOD_TEXT ,
        hh.ADI ,
        ss.STOK_ADI ,
        MIKTAR = ROUND(SUM(CASE WHEN hh.KADEMELI != 'C' THEN GIRIS_CIKIS * BIRIM1_MIKTAR 
			           WHEN hh.KADEMELI = 'C' THEN GIRIS_CIKIS * BIRIM2_MIKTAR 
                               ELSE 0 END), 2) ,
        BIRIM = (SELECT [dbo].[BBGetBirimAcklm](HH.KOD_ID,SH.ANBAR_ID) )		      
FROM    STOK_HAREKET sh ( NOLOCK )
INNER JOIN STOK_STOKLAR ss ( NOLOCK ) ON sh.ANBAR_ID = ss.STOK_ID
LEFT OUTER JOIN SHHSUBE_SECILEMESIN_FLG_VIEW SSFV ( NOLOCK ) ON sh.STOK_ID = SSFV.KOD_ID_SHH
                                                              AND ss.SUBE_ID = SSFV.SUBE_ID_SHH 
INNER JOIN HASTANE_HIZMETLERI hh ( NOLOCK )         
        ON sh.STOK_ID = hh.KOD_ID  AND HH.HIZMET_TIPI = 'S'
INNER JOIN dbo.HIZMET_TURU_ACKLM HTA(NOLOCK)
      ON HH.HIZMET_TURU_ID = HTA.KOD_ID
INNER JOIN dbo.STOK_KARTI_AMBAR SKA(NOLOCK)
      ON SKA.AMBAR_ID = sh.ANBAR_ID AND HH.KOD_ID =SKA.STOK_ID
WHERE        
        sh.ANBAR_ID IN (@@ANBAR_ID@@)
       @@HIZTUR_ID@@
       @@KOD_ID@@
          AND ( sh.ONAY_TARIHI >= CONVERT(DATETIME, '01/01/1900 00:00:00', 101)
              AND sh.ONAY_TARIHI < CONVERT(DATETIME, '01/01/2200 00:00:00', 101)
            )

GROUP BY hh.KOD_ID ,
        hh.KOD_TEXT ,
        hh.ADI ,
        hh.KADEME_ACIKLAMA ,
        ss.STOK_ADI ,hh.KADEMELI,
        ss.STOK_ID ,
        BIRIM1_ID ,
        BIRIM2_ID ,
        BIRIM3_ID ,
        ss.FIRMA_ID ,     
        sh.ANBAR_ID

@@SIFIRDANFARKLI@@

ORDER BY hh.ADI
