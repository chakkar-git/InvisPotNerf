# InvisPotNerfpackage me.kalo121;

import me.kalo121.ConfigInfo;
import java.util.Iterator;
import java.util.logging.Level;
import org.bukkit.ChatColor;
import org.bukkit.command.Command;
import org.bukkit.command.CommandSender;
import org.bukkit.configuration.InvalidConfigurationException;
import org.bukkit.entity.Arrow;
import org.bukkit.entity.Player;
import org.bukkit.event.Listener;
import org.bukkit.event.entity.EntityDamageByEntityEvent;
import org.bukkit.plugin.java.JavaPlugin;
import org.bukkit.potion.PotionEffect;
import org.bukkit.potion.PotionEffectType;

public class InvisPotNerf extends JavaPlugin
    implements Listener
{

    public InvisPotNerf()
    {
    }

    public void onEnable()
    {
        try
        {
            config = new ConfigInfo(this);
            config.init();
        }
        catch(Exception ex)
        {
            getLogger().log(Level.SEVERE, "FAILED TO LOAD CONFIG!!!", ex);
            getServer().getPluginManager().disablePlugin(this);
            return;
        }
        getServer().getPluginManager().registerEvents(this, this);
        super.onEnable();
    }

    public void onDisable()
    {
        try
        {
            config.save();
        }
        catch(InvalidConfigurationException e)
        {
            getLogger().log(Level.SEVERE, "FAILED TO SAVE CONFIG!!!", e);
            e.printStackTrace();
        }
    }

    public boolean onCommand(CommandSender sender, Command command, String label, String args[])
    {
        if((sender instanceof Player) && (label.equalsIgnoreCase("InvisPotNerf") || label.equalsIgnoreCase("InvisPotNerf")))
        {
            if(args.length == 0)
            {
                sender.sendMessage((new StringBuilder()).append(ChatColor.GOLD).append("[InvisPotNerf]").append(ChatColor.GREEN).append(" Developed By kalo121 ;)").toString());
                sender.sendMessage((new StringBuilder()).append(ChatColor.GOLD).append("[InvisPotNerf]").append(ChatColor.GREEN).append(" use (/InvisPotNerf reload) to reload the config").toString());
            }
            if(args.length == 1 && args[0].equalsIgnoreCase("reload") && sender.isOp())
                try
                {
                    config.reload();
                    sender.sendMessage((new StringBuilder()).append(ChatColor.GOLD).append("[InvisPotNerf]").append(ChatColor.GREEN).append(" Config reloaded").toString());
                }
                catch(InvalidConfigurationException e)
                {
                    sender.sendMessage((new StringBuilder()).append(ChatColor.GOLD).append("[InvisPotNerf]").append(ChatColor.RED).append(" Config failed to reload!").toString());
                    getLogger().log(Level.SEVERE, "FAILED TO SAVE CONFIG!!!", e);
                    e.printStackTrace();
                }
        }
        return false;
    }

    public void entityDamage(EntityDamageByEntityEvent event)
    {
        if(event.getDamager() instanceof Player)
        {
            Player player = (Player)event.getDamager();
            if(config.removepotion)
            {
                player.removePotionEffect(PotionEffectType.INVISIBILITY);
                return;
            }
            for(Iterator<PotionEffect> iterator = player.getActivePotionEffects().iterator(); iterator.hasNext();)
            {
                PotionEffect p = (PotionEffect)iterator.next();
                if(p.getType().equals(PotionEffectType.INVISIBILITY))
                {
                    player.removePotionEffect(PotionEffectType.INVISIBILITY);
                    int removetime = config.seconds * 20;
                    int b = p.getDuration() - removetime;
                    player.addPotionEffect(new PotionEffect(PotionEffectType.INVISIBILITY, b, 0));
                }
            }

        }
        if(event.getDamager() instanceof Arrow)
        {
            Arrow Arrow = (Arrow)event.getDamager();
            if(Arrow.getShooter() instanceof Player)
            {
                Player player = (Player)Arrow.getShooter();
                if(config.removepotion)
                {
                    player.removePotionEffect(PotionEffectType.INVISIBILITY);
                    return;
                }
                for(Iterator<PotionEffect> iterator1 = player.getActivePotionEffects().iterator(); iterator1.hasNext();)
                {
                    PotionEffect p = (PotionEffect)iterator1.next();
                    if(p.getType().equals(PotionEffectType.INVISIBILITY))
                    {
                        player.removePotionEffect(PotionEffectType.INVISIBILITY);
                        int removetime = config.seconds * 20;
                        int b = p.getDuration() - removetime;
                        player.addPotionEffect(new PotionEffect(PotionEffectType.INVISIBILITY, b, 0));
                    }
                }

            }
        }
    }

    public ConfigInfo config;
}
